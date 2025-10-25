# Transportation and Logistics System Documentation

## System Architecture Diagrams

### 1. Context Diagram - System Overview

```mermaid
graph TB
    B2C[العميل المباشر<br/>B2C]
    B2B[الشركات والفنادق<br/>B2B]
    Partners[الموردون/السائقون<br/>Partners]
    Admin[مدير النظام<br/>Admin]
    TDS[نظام النقل<br/>والتوصيل]
    External[الجهات الخارجية]

    B2C -->|تأكيد وفوترة| TDS
    B2C -->|طلب حجز| TDS
    
    External -->|تقارير وفواتير| TDS
    External -->|حجز للعملاء| TDS
    External -->|إشعارات حجز| TDS
    External -->|تسجيل سيارات| TDS
    
    Partners -->|تقارير وإحصائيات| TDS
    Admin -->|إدارة وإعدادات| TDS
    
    TDS -->|إشعارات حجز| B2B
    TDS -->|تقارير وفواتير| B2B

    style TDS fill:#f9f,stroke:#333,stroke-width:4px
```

### 2. Technical Architecture

```mermaid
graph TB
    subgraph "External Services خدمات خارجية"
        GoogleMaps[Google Maps API<br/>الخرائط والمسافات]
        Moyasar[Moyasar/Hyperpay<br/>بوابة الدفع]
        Twilio[Twilio/Unifonic<br/>SMS إرسال]
        WhatsApp[WhatsApp API<br/>رسائل واتساب]
    end

    subgraph "Frontend Applications"
        ReactControl[لوحة التحكم<br/>React.js]
        ReactNative[تطبيق الجوال<br/>React Native]
        ReactWeb[تطبيق الويب<br/>React.js]
    end

    subgraph "Backend System الخادم"
        APIServer[API Server<br/>Node.js/Laravel]
        
        subgraph "Modules"
            Auth[نظام المصادقة<br/>JWT]
            Operations[نظام التشغيل]
            Sales[نظام المبيع]
            Versions[نظام الإصدارات]
            Reservations[نظام الحجز]
        end
        
        subgraph "Data Layer"
            MySQL[(MySQL<br/>البيانات الرئيسية)]
            Redis[(Redis<br/>الكاش)]
            GoogleM[GoogleM]
        end
    end

    ReactControl --> APIServer
    ReactNative --> APIServer
    ReactWeb --> APIServer

    APIServer --> Auth
    APIServer --> Operations
    APIServer --> Sales
    APIServer --> Versions
    APIServer --> Reservations

    Auth --> MySQL
    Operations --> MySQL
    Sales --> MySQL
    Reservations --> MySQL
    Reservations --> Redis

    APIServer --> GoogleMaps
    APIServer --> Moyasar
    APIServer --> Twilio
    APIServer --> WhatsApp

    Reservations --> GoogleM

    style APIServer fill:#bbf,stroke:#333,stroke-width:2px
    style MySQL fill:#dfd,stroke:#333,stroke-width:2px
    style Redis fill:#fdd,stroke:#333,stroke-width:2px
```

### 3. Database Schema

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

    TRANSACTION {
        int transaction_id PK
        string commission_id FK
        string credit_number
        boolean processed
        int payment_status
        string request_status
    }

    BOOKING_ITEM {
        int item_id PK
        string booking_id FK
        string car_id FK
        string service_option
        int adults_id FK
        int year_price
        string period_type
        boolean is_active
    }
```

### 4. Booking Flow - Sequence Diagram

```mermaid
sequenceDiagram
    participant Employee as موظف
    participant Client as عميل
    participant B2B as نظام B2B تجارى
    participant Reservation as نظام الحجز
    participant Website as نظام الويب سايت
    participant Operations as نظام التشغيل
    participant Driver as سائق

    Employee->>Client: طلب بيانات التنظير
    Client->>Employee: تسجيل دخول (رقم الهوية)
    Employee->>Client: مرجعة سابقة أخرى
    Client->>B2B: حجز جديد
    B2B->>Reservation: تقديم بيانات الحجز (الأنتر، الرسوم)
    Reservation->>B2B: تعيير الحجوزة والأسعار للعميل (الشركة)
    B2B->>Reservation: إختيار الحجوزة وإرسالها (للاعتماد)
    Reservation->>Website: شحن حد 200 ريال
    Website->>Reservation: تحويل طريقة عمر الحجز (مذا)
    Reservation->>Driver: طباعة عمر الحجز
    Driver->>Reservation: استلام عمر الحجز
    
    rect rgb(70, 130, 180)
        Note over B2B,Driver: الربط بذن لعمل البحث
    end
    
    Reservation->>Operations: تعال الحجز
    Operations->>Driver: اصدار 200 ريال (توصيل أو تعديل)
    
    rect rgb(100, 149, 237)
        Note over Operations: جهة الحجز
    end
    
    Operations->>Website: تقديم العام و السرعية
    Website->>B2B: قدرة الشركة تحكيم 45,800 ريال
    B2B->>Employee: تعيير تقارير بذاتكها
```

### 5. Sales Booking Process

```mermaid
flowchart TD
    Start([بداية - توسل الحجز لطلب الحوار]) --> CheckType{التفاقد المطاف حسب الوقت}
    
    CheckType -->|أنا مالك الوقت الإرجاعة| WaitQueue[الوضوع في حالة الانتظار]
    CheckType -->|لم تكن الفوات الإرجاعة في البداء| Build[تلقائرة المطاف حسب المنطقة]
    
    WaitQueue --> WaitQueue
    
    Build --> Follow{متابعة التنفيذ<br/>من مشرف الشباكة}
    
    Follow -->|مرتبطة| Track[تتبع الرحلة]
    Follow -->|عربية| Process1[الوضع بشن البعيد]
    Follow -->|تامنة لاخرى| Complete[انتتهاء بالمجام الشهري<br/>Akfal]
    
    Track --> LoopCheck{حالة الرحلة}
    LoopCheck -->|جارية| Track
    LoopCheck -->|منتهية| Complete
    
    Process1 --> Complete
    Complete --> Distribution[توزيع المستحقات تلقائيا<br/>نسبة خصم للتوسيل قسمة عملاء لموردة صافي الدخع]
    Distribution --> Send[إرسال الفواتير والتخصيبة<br/>PDF + QR Code]
    Send --> Request[طلب تقييم من العميل]
    Request --> End([النهاية])

    style Complete fill:#6495ED
    style Distribution fill:#1E90FF
```

### 6. Route Planning Flowchart

```mermaid
flowchart TD
    Start([بداية - تخطيط جدول طلب عملاء]) --> LoadData[الخوال المرسلة للعمعيل]
    
    LoadData --> FindStations[تحديد نقاط السنقام<br/>الأطيب والأعقاد<br/>العزل والأقتتصادية للارحز]
    
    FindStations --> CalcDistances[رفع المسافات الفعلية<br/>وفع المسافة الفعلي في الخارج الفعلي في<br/>المدعيات ضمنها]
    
    CalcDistances --> Branches{رفع مسارات العمرة}
    
    Branches -->|صندوق العربة للحباية| DirectBranch[صندوق العربة للحباية]
    Branches -->|صندوق وطنية الأخوة| StationBranch[صندوق وطنية الأخوة]
    
    DirectBranch --> Merge[رباط معطيات الخرائع<br/>وفوق الطريقة الأفضل لجميع<br/>الموسفيل الخبيد بفن]
    StationBranch --> Merge
    
    Merge --> SendOrder[رسل تفصيل الطريقة]
    
    SendOrder --> Approval{التشركات موجبحة؟}
    
    Approval -->|نعم| Accept[رفع الطلب]
    Approval -->|لا| Modify[تحديد سبعة الأخصعرا<br/>والجمت 20]
    
    Accept --> End1([نجح])
    
    Modify --> RetryNotif[ارسل الإخطار الالكتروني بح عمالية]
    RetryNotif --> TrackFinal[التوسفة ويعفضل المالصل]
    TrackFinal --> NotifyDriver[النشهبر الخدور بيتروقع كتها]
    NotifyDriver --> SendInstructions[تسليل التعريوزات<br/>والتحنيم الخدمويزات]
    SendInstructions --> Final([التنهى - مشواءر العمبل])
    Final --> BookingConfirm[حجز يعامل جديول الطلب<br/>الأخعورى]

    style Accept fill:#32CD32
    style Modify fill:#1E90FF
```

### 7. Trip Search in Database

```mermaid
flowchart TD
    Start([بداية - وسول للحجز في قاعدة البابة]) --> ReadFilters[التقرد المطافات حسب الريقت]
    
    ReadFilters -->|أنا مالك وفت الإرجاعة| WaitSide[الوضوع في حالة الانتظار]
    ReadFilters -->|لم تكن الفوات الإرجاعة في البداء| FilterSide[تلقائرة المطاف حسب المنطقة]
    
    WaitSide --> WaitSide
    
    FilterSide --> ExecutionFollow{متابعة التنفيذ<br/>من مشرف الشباكة}
    
    ExecutionFollow -->|منتهية| CompleteTrip[انتتهاء بالمجام الشهري<br/>Akfal]
    
    CompleteTrip --> SendDoc[توزيع المستحقات تلقائيا<br/>نسبة خصم للتوسيل قسمة عملاء لموردة صافي الدخع]
    SendDoc --> QRCode[إرسال الفواتير والتخصيبة<br/>PDF + QR Code]
    QRCode --> FeedbackRequest[طلب تقييم من العميل]
    FeedbackRequest --> End([النهاية])

    style CompleteTrip fill:#4169E1
```

### 8. Route Arrival - Tracking Flow

```mermaid
flowchart TD
    Start([بداية - وسول الحجد الحوا مزية]) --> LoadBooking[ناقذة الحجز<br/>في شاشة الخدمورة المؤكدة]
    
    LoadBooking --> ShowType[تايز عرص التفقيب وفتع الخجز]
    
    ShowType --> ChooseVehicles{طوع المخفورات حسب الشاشلة والقوقت<br/>والوقتت}
    
    ChooseVehicles -->|المخفورات القريبة| NearVehicles[المخفورات القريبة]
    ChooseVehicles -->|المخفورات التي فهر قريب| FarVehicles[المخفورات محاولة المؤدل<br/>رقم عن ربفتكن]
    
    NearVehicles --> ShowSelection[طوء التقابلي الشخصي<br/>حسب الشاحلة والتكليف والوقت]
    FarVehicles --> ShowSelection
    
    ShowSelection --> ValidDistance{طوع أى المخخورك<br/>ضمن 15 كنلم}
    
    ValidDistance -->|طوع عن الشركة| FarAction[رسالة السادس جداول رحلة<br/>منذالية]
    ValidDistance -->|عديم عن المتفق| NearAction[طوع العما في البذع البعد نفكرر<br/>الجمم ممنوحة حضانة الخلتة]
    
    FarAction --> Notify[تكفيل تكلفت]
    NearAction --> DirectAction[طوع التكلت في القدر بين الطلب]
    
    DirectAction --> BookConfirmation[رسل نشعيرت<br/>إغلى]
    Notify --> BookConfirmation
    
    BookConfirmation --> SendDetails[رسل نشعيرت التفريق طلب الحلمة]
    SendDetails --> End([رقم])

    style ValidDistance fill:#228B22
```

### 9. New Booking Arrival

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

    style NotifyRejection fill:#4169E1
```

### 10. Customer Registration Flow

```mermaid
flowchart TD
    Start([بداية - تسجيل جديد عملاء]) --> RegMethod{طريقة تسجيلة}
    
    RegMethod -->|لا| Guest[تسجيل كضيف]
    RegMethod -->|نعم| CreateAccount[انشاء حساب جديد]
    RegMethod -->|ب| Continue[متابعة الخدمة]
    
    Guest --> OTP[إرسال OTP عبر رقم الجوال]
    CreateAccount --> Verify[تسجيل بيانات المرسلة<br/>الإدخال و التحقق للتحقيق]
    Continue --> Verify
    
    OTP --> VerifyData[تحديد تسميع المرسلة<br/>وإدخال الجروح للتحقيق وفتح]
    
    VerifyData --> SelectService{اختيار نوع الخدمة}
    Verify --> SelectService
    
    SelectService -->|بوزر عطن| Type1[بوزر عطن]
    SelectService -->|تيزيد يشاء أو فقر| Type2[تيزيد يشاء أو فقر]
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
    ApprovalCheck -->|نعم| PaymentPage[بوابة الدفع الإلكتروني]
    
    ModifyBooking --> SubmitRequest
    
    PaymentPage --> SendConfirmation[رسل نشعيرت تمكيد<br/>عصيانيل تمامة بلاقخة رمسال]
    
    SendConfirmation --> End([نهاية - اكتميل تسجيل لعمان<br/>الحرخو])

    style VerifyData fill:#4169E1
    style DisplayService fill:#32CD32
```

---