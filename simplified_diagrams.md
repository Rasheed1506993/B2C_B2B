## 1. مخطط السياق المبسط (Context Diagram)

```mermaid
flowchart TB
    subgraph External[الجهات الخارجية]
        C1[العميل المباشر<br/>B2C]
        C2[الشركات والفنادق<br/>B2B]
        C3[الموردون/السائقون<br/>Partners]
        C4[مدير النظام<br/>Admin]
    end
    
    System[نظام النقل<br/>والتوصيل]
    
    C1 -->|طلب حجز| System
    System -->|تأكيد وفاتورة| C1
    
    C2 -->|حجز للعملاء| System
    System -->|تقارير وفواتير| C2
    
    C3 -->|تسجيل سيارات| System
    System -->|إشعارات حجز| C3
    
    C4 -->|إدارة وإعدادات| System
    System -->|تقارير وإحصائيات| C4
    
    style System fill:#4A90E2,color:#fff
    style C1 fill:#50C878,color:#fff
    style C2 fill:#FF6B6B,color:#fff
    style C3 fill:#FFB84D,color:#fff
    style C4 fill:#9B59B6,color:#fff
```

---

## 2. مخطط تدفق عملية الحجز للعميل المباشر

```mermaid
flowchart TD
    Start([بداية - العميل يفتح التطبيق]) --> Login{هل لديك حساب؟}
    
    Login -->|نعم| Login1[تسجيل الدخول]
    Login -->|لا| Guest[متابعة كضيف]
    Login -->|لا| Register[إنشاء حساب جديد]
    
    Register --> Verify[إدخال رمز التحقق OTP]
    Verify --> EnterDetails
    
    Login1 --> EnterDetails[إدخال تفاصيل الرحلة<br/>الموقع، الوجهة، التاريخ]
    Guest --> EnterDetails
    
    EnterDetails --> SelectService[اختيار نوع الخدمة]
    
    SelectService --> ServiceType{نوع الخدمة}
    
    ServiceType -->|توصيل| S1[من مكان لمكان]
    ServiceType -->|إيجار بالساعة| S2[إيجار ساعة أو أكثر]
    ServiceType -->|نصف يوم| S3[8 ساعات]
    ServiceType -->|يوم كامل| S4[12 ساعة]
    ServiceType -->|جولات| S5[بين المدن]
    
    S1 --> SelectCar
    S2 --> SelectCar
    S3 --> SelectCar
    S4 --> SelectCar
    S5 --> SelectCar
    
    SelectCar[اختيار فئة السيارة<br/>سيدان/SUV/باص/لاكجري] --> Calculate[حساب السعر تلقائياً<br/>المسافة × سعر الكم]
    
    Calculate --> ShowPrice[عرض السعر للعميل]
    
    ShowPrice --> Approve{موافق على السعر؟}
    
    Approve -->|لا| SelectService
    Approve -->|نعم| AddToCart{إضافة حجوزات أخرى؟}
    
    AddToCart -->|نعم| EnterDetails
    AddToCart -->|لا| Payment[اختيار طريقة الدفع]
    
    Payment --> PaymentMethod{طريقة الدفع}
    
    PaymentMethod -->|كاش| Confirm
    PaymentMethod -->|مدى| Gateway[بوابة الدفع الإلكتروني]
    PaymentMethod -->|تحويل| Upload[رفع إيصال التحويل]
    
    Gateway --> Confirm[تأكيد الحجز]
    Upload --> Confirm
    
    Confirm --> SMS[إرسال SMS بتفاصيل الحجز]
    SMS --> End([انتهاء - انتظار الموافقة])
    
    style Start fill:#50C878,color:#fff
    style End fill:#50C878,color:#fff
    style Confirm fill:#4A90E2,color:#fff
    style Payment fill:#FFB84D,color:#fff
```
---

## 3. مخطط تدفق عملية التشغيل (للإدارة)
```mermaid
flowchart TD
    Start([بداية - وصول حجز جديد]) --> Receive[استقبال الحجز<br/>في شاشة الحجوزات الواردة]
    
    Receive --> Review[مدير التشغيل يراجع الحجز]
    
    Review --> Decision{قرار المدير}
    
    Decision -->|رفض| Reject[رفض الحجز]
    Reject --> NotifyReject[إشعار العميل بالرفض مع ذكر السبب]
    NotifyReject --> End1([انتهاء])
    
    Decision -->|موافقة| Approve[الموافقة على الحجز]
    
    Approve --> ConfirmPayment[إرسال التأكيد إلى عملية الحجز لتأكيد الدفع]
    
    ConfirmPayment --> SendConfirmed[إرسال الطلب المؤكد إلى شاشة الحجوزات المؤكدة]
    
    SendConfirmed --> End3([انتهاء])
    
    style Start fill:#50C878,color:#fff
    style End1 fill:#E74C3C,color:#fff
    style End3 fill:#50C878,color:#fff
    style Approve fill:#4A90E2,color:#fff
```
---
## 4.
مخطط تدفق
الحجوزات المؤكدة
```mermaid
flowchart TD
    A[وصول الحجز المؤكد - بداية] --> B[استقبال الحجز<br>في شاشة الحجوزات المؤكدة]
    B --> C[نائب مدير التشغيل يراجع الحجز]
    C --> D{التاكد من توفر<br>سائقين متاحين}
    D -->|توفر سائق| E[ربط الحجز مع سائق وسيارة مناسبة]
    D -->|عدم توفر سائق| F{يوجد سائق متاح؟}
    
    F -->|لا| G[وضع في قائمة الانتظار]
    G --> H[إشعار العميل بالانتظار]
    H --> I[انتهاء]
    
    F -->|نعم| E
    
    E --> J[إرسال الإشعارات]
    J --> K[SMS/WhatsApp للسائق<br>بتفاصيل العميل]
    J --> L[SMS/WhatsApp للعميل<br>بتفاصيل السائق والسيارة]
    
    K --> M[إرسال الطلب الى شاشة التنفيذ]
    L --> M
    M --> N[انتهاء]
```
## 5. مخطط تدفق متابعة التنفيذ
```mermaid
flowchart TD
    A[وصول الحجز الى شاشة التنفيذ - بداية] --> B{اذا كان وقت الرحله لم يبداء}
    B -->|نعم| C[الوضع في حالة انتظار]
    B -->|لا| D[متابعة التنفيذ<br>من مشرف المنطقة]
    
    C --> E[فلترة الطلبات حسب المنطقة]
    E --> F[انتهاء]
    
    D --> G{حالة الرحلة}
    G -->|جارية| H[تتبع الرحلة]
    G -->|مكتملة| I[السائق يبلغ بالانتهاء]
    
    H --> I
    I --> J[الإقفال النهائي Akfal]
    J --> K[توزيع المستحقات تلقائياً<br>صافي الربح + نسبة عمولة المورد + نسبة خصم للعميل]
    K --> L[إصدار الفاتورة الضريبية<br>PDF + QR Code]
    L --> M[طلب تقييم من العميل]
    M --> N[انتهاء]
```

## 4. مخطط تدفق تسجيل مورد جديد

```mermaid
flowchart TD
    Start([بداية - مورد جديد]) --> Access[الدخول لصفحة التسجيل]
    
    Access --> PersonalInfo[إدخال البيانات الشخصية<br/>الاسم، رقم الهوية<br/>الجوال، الإيميل]
    
    PersonalInfo --> BankInfo[إدخال بيانات البنك<br/>رقم الحساب البنكي]
    
    BankInfo --> Documents[رفع المستندات المطلوبة]
    
    Documents --> Doc1[صورة الهوية الوطنية]
    Documents --> Doc2[صورة رخصة القيادة]
    
    Doc1 --> VehicleInfo
    Doc2 --> VehicleInfo
    
    VehicleInfo[إدخال معلومات السيارة<br/>رقم اللوحة، الماركة<br/>الموديل، السنة، اللون]
    
    VehicleInfo --> VehicleDoc[رفع مستندات السيارة]
    
    VehicleDoc --> VDoc1[صورة استمارة السيارة]
    VehicleDoc --> VDoc2[صورة التأمين]
    VehicleDoc --> VDoc3[4 صور خارجية للسيارة]
    VehicleDoc --> VDoc4[4 صور داخلية للسيارة]
    
    VDoc1 --> Submit
    VDoc2 --> Submit
    VDoc3 --> Submit
    VDoc4 --> Submit
    
    Submit[إرسال الطلب للمراجعة] --> AdminReview[الإدارة تراجع الطلب]
    
    AdminReview --> CheckDoc{المستندات صحيحة؟}
    
    CheckDoc -->|لا| RejectApp[رفض الطلب]
    NotifyReject --> End1([انتهاء])
    
    CheckDoc -->|نعم| SetCommission[تحديد نسبة العمولة<br/>مثال: 20%]
    
    SetCommission --> SignContract[توقيع الاتفاقية الإلكترونية]
    
    SignContract --> Approve[الموافقة وتفعيل الحساب]
    
    Approve --> NotifyApprove[إشعار المورد بالموافقة]
    
    NotifyApprove --> Activate[تفعيل السيارة<br/>وإتاحتها للحجوزات]
    
    Activate --> End2([انتهاء - جاهز للعمل])
    
    style Start fill:#50C878,color:#fff
    style End1 fill:#E74C3C,color:#fff
    style End2 fill:#50C878,color:#fff
    style Approve fill:#4A90E2,color:#fff
    style Submit fill:#FFB84D,color:#fff
```

---

## 5. مخطط تدفق عملية الدفع
```mermaid
flowchart TD
    Start([بداية - العميل يختار الدفع]) --> SelectMethod[اختيار طريقة الدفع]
    
    SelectMethod --> Method{طريقة الدفع}
    
    Method -->|كاش| Cash[الدفع مع السائق]
    Method -->|مدى| Mada[بوابة الدفع الإلكتروني]
    Method -->|تحويل بنكي| Transfer[التحويل البنكي]
    Method -->|كريديت| Credit[دفع آجل - للشركات فقط]
    
    Cash --> RecordCash[تسجيل طريقة الدفع: كاش]
    RecordCash --> ConfirmBooking
    
    Mada --> Gateway[توجيه لبوابة Moyasar/Hyperpay]
    Gateway --> EnterCard[إدخال بيانات البطاقة]
    EnterCard --> ProcessPayment[معالجة الدفع]
    ProcessPayment --> PaymentStatus{حالة الدفع}
    
    PaymentStatus -->|فشل| Failed[فشل الدفع]
    Failed --> Retry{محاولة مرة أخرى؟}
    Retry -->|نعم| Mada
    Retry -->|لا| CancelBooking[إلغاء الحجز]
    CancelBooking --> End1([انتهاء])
    
    PaymentStatus -->|نجح| Success[نجح الدفع]
    Success --> SaveTransaction[حفظ رقم المعاملة<br/>Transaction ID]
    SaveTransaction --> ConfirmBooking
    
    Transfer --> ShowBankDetails[عرض بيانات البنك<br/>رقم الحساب، IBAN]
    ShowBankDetails --> UploadReceipt[رفع إيصال التحويل]
    UploadReceipt --> PendingVerify[في انتظار التحقق]
    PendingVerify --> AdminCheck{التحقق من التحويل}
    
    AdminCheck -->|لم يتم التحويل| RejectTransfer[رفض الحجز]
    RejectTransfer --> End2([انتهاء])
    
    AdminCheck -->|تم التحويل| ConfirmTransfer[تأكيد التحويل]
    ConfirmTransfer --> ConfirmBooking
    
    Credit --> AddToInvoice[إضافة للفاتورة الشهرية]
    AddToInvoice --> ConfirmBooking
    
    ConfirmBooking[تأكيد الحجز نهائياً] --> GenerateInvoice[إنشاء الفاتورة الضريبية<br/>PDF + QR Code]
    
    GenerateInvoice --> SendSMS[إرسال SMS بتأكيد الحجز<br/>ورقم المرجع]
    
    SendSMS --> End4([انتهاء - حجز مؤكد])
    
    style Start fill:#50C878,color:#fff
    style End1 fill:#E74C3C,color:#fff
    style End2 fill:#E74C3C,color:#fff
    style End4 fill:#50C878,color:#fff
    style Success fill:#4A90E2,color:#fff
    style ConfirmBooking fill:#9B59B6,color:#fff
```

---

## 6. مخطط تسلسلي مبسط: حجز عميل مباشر (Sequence Diagram)

```mermaid
sequenceDiagram
    actor عميل as العميل
    participant تطبيق as التطبيق/الموقع
    participant حجز as نظام الحجز
    participant تسعير as نظام التسعير
    participant دفع as نظام الدفع
    participant تشغيل as نظام التشغيل
    participant سائق as السائق
    
    عميل->>تطبيق: فتح التطبيق
    تطبيق->>عميل: عرض صفحة تسجيل الدخول
    
    عميل->>تطبيق: اختيار "حجز كضيف"
    
    عميل->>حجز: اختيار نوع الخدمة (توصيل)
    حجز->>عميل: عرض فئات السيارات
    
    عميل->>حجز: اختيار سيارة (سيدان)
    حجز->>عميل: طلب تفاصيل الرحلة
    
    عميل->>حجز: إدخال (من، إلى، تاريخ، وقت)
    حجز->>تسعير: حساب السعر
    تسعير->>حجز: السعر = 45 ريال
    حجز->>عميل: عرض السعر
    
    عميل->>حجز: موافق، متابعة للدفع
    حجز->>دفع: طلب الدفع
    دفع->>عميل: عرض طرق الدفع
    
    عميل->>دفع: اختيار "مدى"
    دفع->>عميل: توجيه لبوابة الدفع
    عميل->>دفع: إدخال بيانات البطاقة
    دفع->>دفع: معالجة الدفع
    دفع->>حجز: دفع ناجح ✓
    
    حجز->>تشغيل: إرسال الحجز للمراجعة
    تشغيل->>تشغيل: مدير التشغيل يوافق
    تشغيل->>تشغيل: البحث عن سائق متاح
    تشغيل->>سائق: إرسال إشعار بالحجز
    
    سائق->>تشغيل: قبول الحجز
    تشغيل->>عميل: SMS: تفاصيل السائق والسيارة
    
    Note over عميل,سائق: الرحلة تتم بنجاح
    
    سائق->>تشغيل: إبلاغ بانتهاء الرحلة
    تشغيل->>تشغيل: الإقفال النهائي
    تشغيل->>عميل: طلب تقييم الخدمة
```

---

## 7. مخطط تسلسلي: تسجيل مورد جديد

```mermaid
sequenceDiagram
    actor مورد as المورد الجديد
    participant نظام as نظام التسجيل
    participant قاعدة as قاعدة البيانات
    participant إدارة as الإدارة
    participant إشعار as نظام الإشعارات
    
    مورد->>نظام: فتح صفحة التسجيل
    نظام->>مورد: نموذج التسجيل
    
    مورد->>نظام: إدخال البيانات الشخصية
    مورد->>نظام: رفع صور (هوية، رخصة)
    مورد->>نظام: إدخال بيانات السيارة
    مورد->>نظام: رفع مستندات السيارة
    مورد->>نظام: رفع صور السيارة (8 صور)
    
    مورد->>نظام: إرسال الطلب
    نظام->>قاعدة: حفظ الطلب (حالة: قيد المراجعة)
    نظام->>مورد: تم الإرسال، انتظر المراجعة
    
    نظام->>إدارة: إشعار بطلب جديد
    
    إدارة->>نظام: فتح الطلب
    نظام->>إدارة: عرض كل التفاصيل والمستندات
    
    إدارة->>إدارة: مراجعة المستندات
    
    alt المستندات صحيحة
        إدارة->>نظام: تحديد نسبة العمولة (20%)
        إدارة->>نظام: الموافقة على الطلب
        نظام->>قاعدة: تحديث حالة المورد (مفعّل)
        نظام->>قاعدة: تسجيل السيارة (متاحة)
        نظام->>إشعار: إرسال موافقة
        إشعار->>مورد: SMS: تم قبول طلبك ✓
        مورد->>نظام: تسجيل الدخول
        نظام->>مورد: مرحباً، يمكنك البدء الآن
    else المستندات غير صحيحة
        إدارة->>نظام: رفض الطلب + السبب
        نظام->>قاعدة: تحديث حالة (مرفوض)
        نظام->>إشعار: إرسال رفض
        إشعار->>مورد: SMS: تم رفض طلبك (السبب)
    end
```

---

## 8. مخطط تسلسلي: موظف فندق يحجز لعميل

```mermaid
sequenceDiagram
    actor موظف as موظف الفندق
    actor عميل_فندق as عميل الفندق
    participant نظام as نظام B2B
    participant حجز as نظام الحجز
    participant كريديت as نظام الكريديت
    participant تشغيل as نظام التشغيل
    participant فاتورة as نظام الفوترة
    
    عميل_فندق->>موظف: طلب سيارة للمطار
    
    موظف->>نظام: تسجيل دخول (حساب الفندق)
    نظام->>موظف: مرحباً، صفحة الحجز
    
    موظف->>حجز: حجز جديد
    موظف->>حجز: إدخال بيانات العميل (اسم، غرفة)
    موظف->>حجز: اختيار الخدمة (توصيل للمطار)
    موظف->>حجز: اختيار السيارة (لاكجري)
    موظف->>حجز: التاريخ والوقت
    
    حجز->>موظف: السعر: 200 ريال
    
    موظف->>حجز: اختيار الدفع: كريديت (آجل)
    حجز->>كريديت: التحقق من حد الكريديت
    
    كريديت->>حجز: ✓ متاح (الحد: 50,000)
    حجز->>موظف: تم تأكيد الحجز
    
    حجز->>موظف: طباعة سند الحجز
    موظف->>عميل_فندق: تسليم سند الحجز
    
    حجز->>تشغيل: إرسال للتشغيل
    تشغيل->>تشغيل: ربط مع سائق
    
    Note over عميل_فندق,تشغيل: الرحلة تتم بنجاح
    
    تشغيل->>فاتورة: إقفال الحجز
    فاتورة->>كريديت: إضافة 200 ريال لفاتورة الفندق
    
    Note over كريديت: نهاية الشهر
    كريديت->>فاتورة: إنشاء فاتورة شهرية
    فاتورة->>نظام: فاتورة الشهر: 45,800 ريال
    نظام->>موظف: إشعار بالفاتورة الشهرية
```

---

## 9. مخطط بسيط للعلاقات بين الكائنات (Simplified ERD)

```mermaid
---
config:
  theme: forest
  layout: elk
---
erDiagram
    USER ||--o{ BOOKING : creates
    USER {
        int user_id PK
        string name
        string phone
        string national_id
        string country
        boolean is_active
        decimal discount_rate
        date created_at
    }
    
    COMPANY ||--o{ BOOKING : creates
    COMPANY ||--o{ COMPANY_USER : has
    COMPANY {
        int company_id PK
        string company_name
        string commercial_registration
        string tax_number
        string phone
        string region
        string type
        decimal discount_rate
        decimal credit_limit
        decimal credit_used
        boolean is_active
    }
    
    COMPANY_USER {
        int user_id PK
        int company_id FK
        string name
        string phone
        string role
        boolean is_active
    }
    
    PARTNER ||--|{ VEHICLE : owns
    PARTNER ||--o{ BOOKING : fulfills
    PARTNER {
        int partner_id PK
        string name
        string phone
        string national_id
        string license_number
        string partner_type
        string bank_account
        decimal commission_rate
        boolean is_approved
        boolean is_active
        string contract_file
    }
    
    VEHICLE ||--o{ BOOKING : used_in
    VEHICLE ||--|| VEHICLE_TYPE : categorized_as
    VEHICLE {
        int vehicle_id PK
        int partner_id FK
        int vehicle_type_id FK
        string plate_number
        string brand
        string model
        int year
        string color
        int seats
        boolean is_active
        string registration_file
        string insurance_file
    }
    
    VEHICLE_TYPE ||--o{ PRICING : has_pricing
    VEHICLE_TYPE {
        int type_id PK
        string type_name
        string type_name_en
        string description
        string image_url
        int sort_order
    }
    
    REGION ||--o{ PRICING : defines_pricing
    REGION {
        int region_id PK
        string region_name
        string city
        boolean is_active
    }
    
    PRICING {
        int pricing_id PK
        int region_id FK
        int vehicle_type_id FK
        int service_type_id FK
        decimal price_per_km
        decimal fixed_price
        date effective_from
        date effective_to
    }
    
    SERVICE_TYPE ||--o{ BOOKING : defines
    SERVICE_TYPE ||--o{ PRICING : has_pricing
    SERVICE_TYPE {
        int service_id PK
        string service_name
        string service_name_en
        int duration_hours
        string description
        boolean is_active
    }
    
    BOOKING ||--|| PAYMENT : has
    BOOKING ||--|| OPERATION : managed_by
    BOOKING ||--o{ BOOKING_ITEM : contains
    BOOKING {
        int booking_id PK
        string booking_number
        int user_id FK
        int company_id FK
        date booking_date
        string status
        decimal total_amount
        decimal discount_amount
        decimal final_amount
    }
    
    BOOKING_ITEM {
        int item_id PK
        int booking_id FK
        int service_type_id FK
        int vehicle_id FK
        string pickup_location
        string destination
        datetime pickup_datetime
        decimal distance_km
        decimal item_price
        string status
    }
    
    PAYMENT {
        int payment_id PK
        int booking_id FK
        string payment_method
        decimal amount
        string payment_status
        string transaction_id
        datetime payment_date
        string receipt_url
    }
    
    OPERATION {
        int operation_id PK
        int booking_id FK
        int assigned_by FK
        int partner_id FK
        string operation_status
        datetime assigned_at
        datetime started_at
        datetime completed_at
        datetime closed_at
        decimal partner_commission
        decimal company_revenue
        boolean is_closed
        string notes
    }
    
    ADMIN ||--o{ OPERATION : manages
    ADMIN ||--o{ REPORT : generates
    ADMIN ||--o{ PERMISSION : has
    ADMIN {
        int admin_id PK
        string name
        string email
        string phone
        string password
        string role
        boolean is_active
    }
    
    PERMISSION {
        int permission_id PK
        int admin_id FK
        string module_name
        boolean can_view
        boolean can_add
        boolean can_edit
        boolean can_delete
        boolean can_approve
    }
    
    REPORT {
        int report_id PK
        int admin_id FK
        string report_type
        string report_title
        date from_date
        date to_date
        string file_url
        datetime generated_at
    }
    
    NOTIFICATION ||--o{ USER : sent_to
    NOTIFICATION ||--o{ PARTNER : sent_to
    NOTIFICATION {
        int notification_id PK
        int recipient_id FK
        string recipient_type
        string notification_type
        string title
        string message
        boolean is_read
        datetime sent_at
    }
    
    RATING ||--|| BOOKING : rates
    RATING {
        int rating_id PK
        int booking_id FK
        int user_id FK
        int driver_rating
        int service_rating
        int vehicle_rating
        string comment
        datetime created_at
    }
    
    FINANCIAL_TRANSACTION ||--|| PARTNER : belongs_to
    FINANCIAL_TRANSACTION {
        int transaction_id PK
        int partner_id FK
        int booking_id FK
        string transaction_type
        decimal amount
        string description
        datetime transaction_date
        string status
    }
    
    SYSTEM_SETTINGS {
        int setting_id PK
        string company_logo
        string company_name
        string tax_number
        string about_us
        string social_media_links
        string whatsapp_number
        string invoice_header
        string invoice_footer
        string theme_color
    }
```
---

## 10. خريطة النظام الكاملة (System Map)

```mermaid
graph TB
    subgraph Frontend["الواجهات الأمامية"]
        WebApp[تطبيق الويب<br/>React.js]
        MobileApp[تطبيق الجوال<br/>React Native]
        AdminPanel[لوحة التحكم<br/>React.js]
    end
    
    subgraph Backend["الخادم Backend"]
        API[API Server<br/>Node.js/Laravel]
        Auth[نظام المصادقة<br/>JWT]
        BookingSys[نظام الحجز]
        PaymentSys[نظام الدفع]
        OperationSys[نظام التشغيل]
        NotifSys[نظام الإشعارات]
    end
    
    subgraph Database["قاعدة البيانات"]
        MySQL[(MySQL<br/>البيانات الرئيسية)]
        Redis[(Redis<br/>الكاش)]
    end
    
    subgraph External["خدمات خارجية"]
        GoogleMaps[Google Maps API<br/>الخرائط والمسافات]
        PaymentGW[Moyasar/Hyperpay<br/>بوابة الدفع]
        SMS[Twilio/Unifonic<br/>إرسال SMS]
        WhatsApp[WhatsApp API<br/>رسائل واتساب]
    end
    
    WebApp --> API
    MobileApp --> API
    AdminPanel --> API
    
    API --> Auth
    API --> BookingSys
    API --> PaymentSys
    API --> OperationSys
    API --> NotifSys
    
    BookingSys --> MySQL
    PaymentSys --> MySQL
    OperationSys --> MySQL
    Auth --> MySQL
    
    API --> Redis
    
    BookingSys --> GoogleM