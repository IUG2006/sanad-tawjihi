# مخطط حالات الاستخدام — سند توجيهي

## الغرض

تحديد ممثلي النظام الرئيسيين والثانويين ووظائف MVP الموثقة، مع إظهار حدود النظام والعلاقات بين حالات الاستخدام عند وجود اعتماد فعلي.

## مصادر المتطلبات

- PRD: الأقسام 5 و8–11 و14–17، والأدوار والصلاحيات، والتكاملات.
- تدفق المستخدم: تدفق التدريب والشحن والامتحان، وتحليل المتطلبات المؤكدة.
- ERD/DBML: حالات محاولات الامتحان ومعاملات المحفظة ومستخدم المدير.

## الرسم (PlantUML)

```plantuml
@startuml
left to right direction

actor "زائر تجريبي" as Guest
actor "طالب مسجل" as Student
actor "مدير النظام" as Admin
actor "مزود Google OAuth" as Google
actor "بوت تيليجرام" as Telegram
actor "واتساب / مشاركة الجهاز" as Share

rectangle "نظام سند توجيهي (MVP)" {
  usecase "استعراض المنهاج\nحسب المنطقة والفرع" as Browse
  usecase "اختيار أو تبديل\nالمسار الإقليمي" as SelectRegion
  usecase "حل كويز أول درسين\nمجاناً" as FreeQuiz
  usecase "حل كويز درس أو وحدة\nمدفوع" as PaidQuiz
  usecase "إجابة سؤال وعرض\nالتصحيح والشرح" as Practice
  usecase "خصم تكلفة كل محاولة\nمدفوعة وحفظها" as SaveQuizAttempt
  usecase "تجربة الاختبارات\nلأغراض الإدارة" as AdminTest
  usecase "إدارة دفتر الأخطاء\nوالتدرب عليها" as Mistakes
  usecase "بدء أو استئناف امتحان شامل\nموقوت وتسليمه" as Exam
  usecase "استئناف المحاولة النشطة\nأو بدء محاولة جديدة" as ResumeExam
  usecase "تأكيد تسليم الامتحان" as ConfirmSubmit
  usecase "التحقق من التسجيل\nوالرصيد" as CheckEligibility
  usecase "طلب شحن نقاط" as Recharge
  usecase "التحقق من بيانات\nالحوالة وعدم تكرارها" as ValidateRecharge
  usecase "إشعار المدير بطلب\nالشحن" as NotifyAdmin
  usecase "مراجعة طلب الشحن\nواعتماده أو رفضه" as ReviewRecharge
  usecase "تسجيل الدخول إلى\nلوحة التحكم" as AdminDashboard
  usecase "إدارة المنهاج وبنك\nالأسئلة والأسعار" as ManageContent
  usecase "مراجعة نتيجة الامتحان\nومحاولاته السابقة" as ReviewResults
  usecase "مشاركة النتيجة نصياً" as ShareResult
  usecase "التسجيل أو تسجيل الدخول" as SignIn
}

Guest --> Browse
Guest --> SelectRegion
Guest --> FreeQuiz
Guest --> SignIn
Student --> Browse
Student --> SelectRegion
Student --> FreeQuiz
Student --> PaidQuiz
Student --> Mistakes
Student --> Exam
Student --> Recharge
Student --> ReviewResults
Student --> ShareResult
Admin --> Browse
Admin --> SelectRegion
Admin --> FreeQuiz
Admin --> AdminTest
Admin --> ManageContent
Admin --> ReviewRecharge
Admin --> ReviewResults

PaidQuiz ..> CheckEligibility : <<include>>
PaidQuiz ..> SaveQuizAttempt : <<include>>
Exam ..> CheckEligibility : <<extend>> [عند عدم وجود محاولة نشطة]
Exam ..> ResumeExam : <<extend>> [عند وجود محاولة نشطة]
Exam ..> ConfirmSubmit : <<extend>> [عند طلب التسليم]
FreeQuiz ..> Practice : <<include>>
PaidQuiz ..> Practice : <<include>>
Recharge ..> ValidateRecharge : <<include>>
Recharge ..> NotifyAdmin : <<include>>
Recharge ..> Exam : <<extend>> [عند عدم كفاية الرصيد]
Recharge ..> PaidQuiz : <<extend>> [عند عدم كفاية الرصيد]
ReviewRecharge ..> AdminDashboard : <<include>>

Google --> SignIn
Telegram --> NotifyAdmin
Share --> ShareResult

note right of Guest
  الزائر يتصفح ويحل المجاني فقط.
  بياناته تحفظ محلياً بحسب PRD.
end note

note bottom of Admin
  المدير هو الدور الإداري الموثق؛
  تشمل صلاحياته إدخال المحتوى ومراجعة الشحن
  من لوحة التحكم بعد تسجيل الدخول.
end note

note right of Telegram
  إشعار بوصول طلب الشحن فقط؛
  لا اعتماد أو رفض عبر تيليجرام.
end note

@enduml
```

## الشرح والتحقق

- الممثلون الأساسيون هم الزائر والطالب المسجل والمدير. لكل منهم وظائفه كما في جدول الصلاحيات في PRD.
- تشمل حالة الكويز الإجابة والتصحيح والشرح؛ لذلك يدرجها كل من الكويز المجاني والمدفوع. يختبر المدير الاختبارات لأغراض الإدارة، ولا يُفترض أن تنطبق عليه شروط نقاط الطالب.
- Google OAuth وبوت تيليجرام ممثلان ثانويان لأن PRD يذكرهما كتدخلين خارجيين صريحين. لا يُمثل مزود دفع خارجياً؛ الدفع في MVP يدوي والمطابقة بيد المدير.
- التحقق من التسجيل والرصيد جزء إلزامي من فتح الاختبار المدفوع. وإذا لم يكف الرصيد، يمكن أن يبدأ الطالب طلب شحن؛ لذلك يظهر الشحن امتداداً مشروطاً لا خطوة إلزامية لكل امتحان. أما مراجعة المدير فهي متابعة لاحقة مستقلة للطلب.
- التحقق من بيانات الشحن وإشعار المدير مشمولان بطلب الشحن. المراجعة الإدارية لا تحدث إلا بعد إرسال الطلب.
- كل محاولة كويز مدفوعة تُخصم تكلفتها وتُحفظ. ويحفظ النظام محاولات الكويز المجانية للزائر محلياً.
- طلب بدء امتحان لنموذج له محاولة نشطة يستأنفها ولا ينشئ محاولة أخرى أو يخصم نقاطاً جديدة.
- إعادة إرسال طلب شحن مرفوض تعدّل السجل نفسه وتحافظ على معرّفه.
- مشاركة النتيجة مرتبطة بواجهة المشاركة/واتساب المذكورة في PRD. لم يُضف ولي الأمر كممثل لأن الوثائق لا تمنحه حالة استخدام أو صلاحية محددة في MVP.
- لم يُضف Include/Extend لمجرد التصنيف؛ العلاقات المرسومة تصف اعتماداً أو شرطاً موثقاً.

## الافتراضات

- يفترض بدء حالات الاستخدام المدفوعة وجود تسجيل دخول صالح؛ ويتم التحقق من ذلك ضمن أهلية الاستخدام. خيار التسجيل بالبريد أو Google موثق، لكن مزود OAuth فقط ممثل كجهة خارجية.
- يعامل المدير ممثلاً واحداً يملك صلاحيات إدارة المحتوى ومراجعة الشحن؛ لا توجد أدوار منفصلة مؤكدة لمعلم أو مدقق.
- المقصود بـ «مشاركة الجهاز» هو Web Share API أو رابط واتساب المذكوران في PRD، لا تكامل مخصص غير موثق.

## الأسئلة المفتوحة

- ولي الأمر ضمن الجمهور المستهدف، لكن هل يحتاج إلى حالات استخدام أو حساب مستقل في MVP؟
- يصف PRD بعض وظائف المدير بأنها «إضافة وتعديل» وأخرى بأنها تعديل تكلفة ومجانية؛ هل تشمل إدارة نماذج الامتحان أيضاً؟
- هل كويز الأخطاء يُعامل كحالة استخدام مستقلة عن التدريب، وهل تحفظ محاولاته؟
