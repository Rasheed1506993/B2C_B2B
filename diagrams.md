# نظام النقل والتوصيل - المخططات الكاملة

---

## 1. مخطط السياق (Context Diagram)

```mermaid
flowchart LR
    B2C[العميل المباشر<br/>B2C]
    B2B[الشركات والفنادق<br/>B2B]
    Partners[الموردون/السائقون<br/>Partners]
    Admin[مدير النظام<br/>Admin]
    System[نظام النقل<br/>والتوصيل]
    External[الجهات الخارجية]
    
    B2C -->|تأكيد وفاتورة| System
    B2C -->|طلب حجز| System
    
    System -->|طلب حجز| External
    External -->|تقارير وفواتير| System
    External -->|حجز للعملاء| System
    External -->|إشعارات حجز| System
    External -->|تسجيل سيارات| System
    
    Partners -->|تقارير وإحصائيات| System
    Partners -->|حجز للعملاء| System
    Partners -->|إشعارات حجز| System
    
    Admin -->|إدارة وإعدادات| System
    
    System -->|إشعارات حجز| B2B
    System -->|تقارير وفواتير| B2B

    style System fill:#e8e8e8,stroke:#333,stroke-width:3px
    style B2C fill:#fff,stroke:#333,stroke-width:2px
    style B2B fill:#fff,stroke:#333,stroke-width:2px
    style Partners fill:#fff,stroke:#333,stroke-width:2px
    style Admin fill:#fff,stroke:#333,stroke-width:2px
    style External fill:#fff,stroke:#333,stroke-width:2px
```

---

## 2. البنية التقنية (Technical Architecture)

```mermaid
flowchart TB
    subgraph "خدمات خارجية"
        GoogleMaps[Google Maps API<br/>الخرائط والمسافات]
        Moyasar[Moyasar/Hyperpay<br/>بوابة الدفع]
        Twilio[Twilio/Unifonic<br/>SMS إرسال]
        WhatsApp[WhatsApp API<br/>رسائل واتساب]
    end

    subgraph "Frontend"
        ReactControl[لوحة التحكم<br/>React.js]
        ReactNative[تطبيق الجوال<br/>React Native]
        ReactWeb[تطبيق الويب<br/>React.js]
    end

    subgraph "Backend الخادم"
        APIServer[API Server<br/>Node.js/Laravel]
        
        JWT[نظام المصادقة<br/>JWT]
        Operations[نظام التشغيل]
        Sales[نظام المبيع]
        Versions[نظام الإصدارات]
        Reservations[نظام الحجز]
        
        MySQL[(MySQL<br/>البيانات الرئيسية)]
        Redis[(Redis<br/>الكاش)]
        GoogleM[GoogleM]
    end

    ReactControl --> APIServer
    ReactNative --> APIServer
    ReactWeb --> APIServer

    APIServer --> JWT
    APIServer --> Operations
    APIServer --> Sales
    APIServer --> Versions
    APIServer --> Reservations

    JWT --> MySQL
    Operations --> MySQL
    Sales --> MySQL
    Reservations --> MySQL
    Reservations --> Redis

    APIServer --> GoogleMaps
    APIServer --> Moyasar
    APIServer --> Twilio
    APIServer --> WhatsApp

    Reservations --> GoogleM

    style APIServer fill:#e8e8e8,stroke:#333,stroke-width:2px
```

---

## 3. قاعدة البيانات (Database Schema)

```mermaid
erDiagram
    NOTIFICATION ||--o{ FINANCIAL_TRANSACTION : has
    FINANCIAL_TRANSACTION ||--o{ PARTNERS : belongs_to
    
    PARTNERS ||--o{ CURRENT : manages
    CURRENT ||--o{ CAR : owns
    
    CAR ||--o{ ORDERS : used_in
    ORDERS ||--o{ RATING : receives
    ORDERS ||--o{ SERVICE_TYPE : has
    
    PARTNERS {
        int partner_id PK
        string name
        string email
        string phone
        int balance_id
        string phone_number
        string partner_type
        boolean bank_account
        string iban
        boolean commercial_unit
        boolean is_approved
        string id_photo
        int contract_id
    }

    CURRENT {
        int current_id PK
        string name
        string email
        string phone
        int partner_id FK
        string balance_id
        int no_number
        string phone
        int status
        boolean is_active
        string action
        int discount_rate
        string cover_photo
        boolean verified
        int contract_id
    }

    CAR {
        int car_id PK
        string brand
        string model
        string seats
        int partner_id FK
        string balance
        boolean is_active
        string plates
    }

    ORDERS {
        int order_id PK
        string status
        string service_id FK
        string price_option
        decimal price
        string from
        string to
        datetime date
        decimal distance
        string payment_type
        int car_id FK
        int action
    }

    BOOKING {
        int booking_id PK
        string booking_number
        string name
        string from
        string to
        datetime booking_date
        string from_address
        string to_address
        decimal price
        int car_id FK
        int current_id FK
        int order_id FK
        boolean is_deleted
    }

    OPERATION {
        int operation_id PK
        string operation_name
        int booking_id FK
        string company_id FK
        string from
        string location_address
        datetime operation_date
        string period_type
        int driver_id FK
        string note_id FK
        int created_by FK
        datetime created_at
        boolean is_deleted
        boolean is_confirmed
        string transfer_location
        string transfer_time
        boolean is_active
    }

    COMPANY_USER {
        int user_id PK
        string company_id FK
        string name
        string email
        string phone
        string address
    }

    VEHICLE_TYPE {
        int type_id PK
        string type_name
        string description
        int seats
    }

    SERVICE_TYPE {
        int service_id PK
        string service_name
        string service_name_ar
        int compact_hours
        string description
    }

    REGION {
        int region_id PK
        string region_ar
        string title
        int city
        decimal price
        string coordinates
    }

    RATING {
        int rating_id PK
        string rating_ar
        string type_id FK
        decimal price
        int stars
        string comment
    }

    PAYMENT {
        int payment_id PK
        string payment_type
        decimal amount
        string booking_id FK
        datetime payment_date
        int sender_id FK
        int receiver_id FK
        string payment_method
        string moyasar_id
    }
```

---

## 4. مخطط تسلسل الحجز (Booking Sequence)

```mermaid
sequenceDiagram
    participant موظف
    participant عميل
    participant B2B as نظام B2B تجاري
    participant حجز as نظام الحجز
    participant ويب as نظام الويب سايت
    participant تشغيل as نظام التشغيل
    participant سائق

    موظف->>عميل: طلب بيانات التسجيل
    عميل->>موظف: تسجيل دخول (رقم العميل)
    موظف->>عميل: مراجعة سابقة (أخرى)
    عميل->>B2B: حجز جديد
    B2B->>حجز: تقديم بيانات الحجز (الأسعار، الرسوم)
    حجز->>B2B: تغيير الحجوزة والأسعار للعميل (الشركة)
    B2B->>حجز: اختيار الحجوزة وإرسالها (للاعتماد)
    حجز->>ويب: شحن حد 200 ريال
    ويب->>حجز: تحويل طريقة عمر الحجز (مدى)
    حجز->>سائق: طباعة عمر الحجز
    سائق->>حجز: استلام عمر الحجز
    
    rect rgb(70, 130, 180)
        Note over B2B,سائق: الرابط بين لعمل البحث
    end
    
    حجز->>تشغيل: تعادل الحجز
    تشغيل->>سائق: إصدار 200 ريال (توصيل أو تعديل)
    
    rect rgb(100, 149, 237)
        Note over تشغيل: جهة الحجز
    end
    
    تشغيل->>ويب: تقديم العام و السرعية
    ويب->>B2B: قدرة الشركة تحكيم 45,800 ريال
    B2B->>موظف: تغيير تقارير بذاتها
```

---

## 5. عملية البيع والحجز (Sales Process)

```mermaid
flowchart TD
    Start([بداية - توصل الحجز لطلب الحوار]) --> CheckType{الفاعد المطاف حسب الوقت}
    
    CheckType -->|أنا ماللك الوقت الارجاعة| WaitQueue[الوضوع في حالة الانتظار]
    CheckType -->|لم تكن الفوات الارجاعة في البداء| Build[تلقائية المطاف حسب المنطقة]
    
    WaitQueue --> WaitQueue
    
    Build --> Follow{متابعة التنفيذ<br/>من مشرف الشباكة}
    
    Follow -->|مرتبطة| Track[تتبع الرحلة]
    Follow -->|عربية| Process1[الوضع بشن البعيد]
    Follow -->|تامنة لاخرى| Complete[انتهاء بالمجام الشهري<br/>Akfal]
    
    Track --> LoopCheck{حالة الرحلة}
    LoopCheck -->|جارية| Track
    LoopCheck -->|منتهية| Complete
    
    Process1 --> Complete
    Complete --> Distribution[توزيع المستحقات تلقائيا<br/>نسبة خصم للتوصيل قسمة عمولة لموردة صافي الدخل]
    Distribution --> Send[إرسال الفواتير والتخصيبة<br/>PDF + QR Code]
    Send --> Request[طلب تقييم من العميل]
    Request --> End([النهاية])

    style Complete fill:#6495ED,color:#fff
    style Distribution fill:#1E90FF,color:#fff
```

---

## 6. تخطيط المسارات (Route Planning)

```mermaid
flowchart TD
    Start([بداية - تخطيط جدول طلب عملاء]) --> LoadData[الحوال المرسلة للعميل]
    
    LoadData --> FindStations[تحديد نقاط السنقام<br/>الاطيب والاعقاد<br/>الغزل والاقتصادية للارجز]
    
    FindStations --> CalcDistances[رفع المسافات الفعلية<br/>وفع المسافة الفعلي في الخارج الفعلي في<br/>المدعيات ضمنها]
    
    CalcDistances --> Branches{رفع مسارات العمرة}
    
    Branches -->|صندوق العربة للحباية| DirectBranch[صندوق العربة للحباية]
    Branches -->|صندوق وطنية الاخوة| StationBranch[صندوق وطنية الاخوة]
    
    DirectBranch --> Merge[رباط معطيات الخرائط<br/>وفوق الطريقة الافضل لجميع<br/>الموسفيل الخبيد بفن]
    StationBranch --> Merge
    
    Merge --> SendOrder[رسل تفصيل الطريقة]
    
    SendOrder --> Approval{التشركات موجبحة؟}
    
    Approval -->|نعم| Accept[رفع الطلب]
    Approval -->|لا| Modify[تحديد سبعة الاخصعرا<br/>والجمت 20]
    
    Accept --> End1([نجح])
    
    Modify --> RetryNotif[ارسل الاخطار الالكتروني بح عمالية]
    RetryNotif --> TrackFinal[التوصفة ويعفضل المالصل]
    TrackFinal --> NotifyDriver[النشهبر الخدور بيتروقع كتها]
    NotifyDriver --> SendInstructions[تسليل التعريوزات<br/>والتحنيم الخدمويزات]
    SendInstructions --> Final([التنهى - مشواءر العمبل])
    Final --> BookingConfirm[حجز يعامل جديول الطلب<br/>الاخعورى]

    style Accept fill:#32CD32,color:#fff
    style Modify fill:#1E90FF,color:#fff
```

---

## 7. البحث عن الرحلة (Trip Search)

```mermaid
flowchart TD
    Start([بداية - وصول للحجز في قاعدة البيانة]) --> ReadFilters[التقرد المطافات حسب الريقت]
    
    ReadFilters -->|انا ماللك وفت الارجاعة| WaitSide[الوضوع في حالة الانتظار]
    ReadFilters -->|لم تكن الفوات الارجاعة في البداء| FilterSide[تلقائية المطاف حسب المنطقة]
    
    WaitSide --> WaitSide
    
    FilterSide --> ExecutionFollow{متابعة التنفيذ<br/>من مشرف الشباكة}
    
    ExecutionFollow -->|منتهية| CompleteTrip[انتهاء بالمجام الشهري<br/>Akfal]
    
    CompleteTrip --> SendDoc[توزيع المستحقات تلقائيا<br/>نسبة خصم للتوصيل قسمة عمولة لموردة صافي الدخل]
    SendDoc --> QRCode[إرسال الفواتير والتخصيبة<br/>PDF + QR Code]
    QRCode --> FeedbackRequest[طلب تقييم من العميل]
    FeedbackRequest --> End([النهاية])

    style CompleteTrip fill:#4169E1,color:#fff
```

---

## 8. تتبع وصول المسار (Route Arrival Tracking)

```mermaid
flowchart TD
    Start([بداية - وصول الحجد الحوا مزية]) --> LoadBooking[ناقذة الحجز<br/>في شاشة الخدمورة المؤكدة]
    
    LoadBooking --> ShowType[تايز عرص التفقيب وفتع الخجز]
    
    ShowType --> ChooseVehicles{طوع المخفورات حسب الشاشلة والقوقت<br/>والوقتت}
    
    ChooseVehicles -->|المخفورات القريبة| NearVehicles[المخفورات القريبة]
    ChooseVehicles -->|المخفورات التي فهر قريب| FarVehicles[المخفورات محاولة المؤدل<br/>رقم عن ربفتكن]
    
    NearVehicles --> ShowSelection[طوء التقابلي الشخصي<br/>حسب الشاحلة والتكليف والوقت]
    FarVehicles --> ShowSelection
    
    ShowSelection --> ValidDistance{طوع اى المخخورك<br/>ضمن 15 كنلم}
    
    ValidDistance -->|طوع عن الشركة| FarAction[رسالة السادس جداول رحلة<br/>منذالية]
    ValidDistance -->|عديم عن المتفق| NearAction[طوع العما في البذع البعد نفكرر<br/>الجمم ممنوحة حضانة الخلتة]
    
    FarAction --> Notify[تكفيل تكلفت]
    NearAction --> DirectAction[طوع التكلت في القدر بين الطلب]
    
    DirectAction --> BookConfirmation[رسل نشعيرت<br/>اغلى]
    Notify --> BookConfirmation
    
    BookConfirmation --> SendDetails[رسل نشعيرت التفريق طلب الحلمة]
    SendDetails --> End([رقم])

    style ValidDistance fill:#228B22,color:#fff
```

---

## 9. وصول حجز جديد (New Booking Arrival)

```mermaid
flowchart TD
    Start([بداية - وصول حجز جديد]) --> ReceiveBooking[استقبال الحجز<br/>في شاشة الحجوزات الواردة]
    
    ReceiveBooking --> ManagerReview[مدير التشغيل يراجع الحجز]
    
    ManagerReview --> Decision{قرار المدير}
    
    Decision -->|موافقة| ApproveBooking[الموافقة على الحجز]
    Decision -->|رفض| RejectBooking[رفض الحجز]
    
    RejectBooking --> NotifyRejection[إشعار العميل بالرفض مع ذكر السبب]
    NotifyRejection --> End1([انتهاء])
    
    ApproveBooking --> SendConfirmation[ارسال التأكيد الى عملية الحجز<br/>لتأكيد الدفع]
    
    SendConfirmation --> SendToScreen[ارسال الطلب المؤكد الى شاشة الحجوزات<br/>المؤكدة]
    
    SendToScreen --> End2([اكتمل])

    style NotifyRejection fill:#4169E1,color:#fff
```

---

## 10. تسجيل عميل جديد (Customer Registration)

```mermaid
flowchart TD
    Start([بداية - تسجيل جديد عملاء]) --> RegMethod{طريقة تسجيلة}
    
    RegMethod -->|لا| Guest[تسجيل كضيف]
    RegMethod -->|نعم| CreateAccount[انشاء حساب جديد]
    RegMethod -->|ب| Continue[متابعة الخدمة]
    
    Guest --> OTP[إرسال OTP عبر رقم الجوال]
    CreateAccount --> Verify[تسجيل بيانات المرسلة<br/>الادخال و التحقق للتحقيق]
    Continue --> Verify
    
    OTP --> VerifyData[تحديد تسميع المرسلة<br/>وادخال الجروح للتحقيق وفتح]
    
    VerifyData --> SelectService{اختيار نوع الخدمة}
    Verify --> SelectService
    
    SelectService -->|بوزر عطن| Type1[بوزر عطن]
    SelectService -->|تيزيد يشاء او فقر| Type2[تيزيد يشاء او فقر]
    SelectService -->|مشاعات 5| Type3[مشاعات 5]
    SelectService -->|شدولة 12| Type4[شدولة 12]
    SelectService -->|بوير الحرض| Type5[بوير الحرض]
    
    Type1 --> DisplayService[تظهير هذا الخدمة<br/>مع التكالفي ونولامججومين]
    Type2 --> DisplayService
    Type3 --> DisplayService
    Type4 --> DisplayService
    Type5 --> DisplayService
    
    DisplayService --> SubmitRequest[تسجيل الخدع الطلب<br/>من تضهيم محملو]
    
    SubmitRequest --> ConfirmRequest[شراكس الخدع الطلب]
    
    ConfirmRequest --> ApprovalCheck{موافق على الشروط؟}
    
    ApprovalCheck -->|لا| ModifyBooking[مدجود تروديدا لبرح]
    ApprovalCheck -->|نعم| PaymentPage[بوابة الدفع الالكتروني]
    
    ModifyBooking --> SubmitRequest
    
    PaymentPage --> SendConfirmation[رسل نشعيرت تمكيد<br/>عصيانيل تمامة بلاقخة رمسال]
    
    SendConfirmation --> End([نهاية - اكتميل تسجيل لعمان<br/>الحرخو])

    style VerifyData fill:#4169E1,color:#fff
    style DisplayService fill:#32CD32,color:#fff
```

---
