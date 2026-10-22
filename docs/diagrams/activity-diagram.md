# مخطط النشاط — سند توجيهي

الرسومات التالية كلها في هذا الملف، ومقسمة إلى ثلاثة أقسام لتسهيل القراءة.

## القسم الأول: كويز التدريب

يوضح اختيار الكويز، والتحقق من المجانية أو الرصيد، ثم عرض النتيجة بعد إنهاء الحل.

```mermaid
flowchart TD
    Start([استعراض المنهاج]) --> SelectQuiz[اختيار كويز درس أو وحدة]
    SelectQuiz --> FreeCheck{هل كويز الدرس ضمن أول درسين؟}
    FreeCheck -->|نعم| OpenFree[فتح الكويز مجاناً]
    FreeCheck -->|لا، كويز مدفوع| QuizSignIn{هل الطالب مسجل؟}
    QuizSignIn -->|لا| SignIn[التسجيل أو تسجيل الدخول]
    QuizSignIn -->|نعم| QuizBalance
    SignIn --> QuizBalance{هل الرصيد يغطي تكلفة المحاولة؟}
    QuizBalance -->|لا| QuizRecharge{هل يريد شحن الرصيد؟}
    QuizRecharge -->|لا، إلغاء| CancelQuiz([العودة إلى المنهاج دون بدء الكويز])
    QuizRecharge -->|نعم| Recharge([الانتقال إلى القسم الثاني: شحن الرصيد])
    QuizBalance -->|نعم| DeductQuiz[خصم سعر الكويز لكل محاولة]
    DeductQuiz --> SaveQuiz[بدء سجل المحاولة دون حفظ الإجابات]
    OpenFree --> FreeRegistered{هل الطالب مسجل؟}
    FreeRegistered -->|نعم| SaveFreeSummary[بدء سجل المحاولة دون حفظ الإجابات]
    FreeRegistered -->|زائر| SaveLocal[بدء محاولة مجانية وحفظها محلياً]
    SaveFreeSummary --> QuizAnswer
    SaveLocal --> QuizAnswer
    SaveQuiz --> QuizAnswer
    QuizAnswer[اختيار إجابة وحفظها مؤقتاً دون كشف صحتها] --> MoreQuestions{هل بقيت أسئلة؟}
    MoreQuestions -->|نعم| QuizAnswer
    MoreQuestions -->|لا| FinishQuiz[إنهاء الكويز وإرسال الإجابات للتقييم]
    FinishQuiz --> GradeQuiz[تقييم الإجابات على الخادم دون حفظها مفردة]
    GradeQuiz --> RegisteredQuiz{هل الطالب مسجل؟}
    RegisteredQuiz -->|نعم| SaveQuizSummary[حفظ ملخص المحاولة وتحديث الأخطاء]
    RegisteredQuiz -->|زائر| SaveGuestQuiz[حفظ النتيجة والأخطاء محلياً]
    SaveQuizSummary --> QuizResult
    SaveGuestQuiz --> QuizResult
    QuizResult[عرض النتيجة والتفسيرات لجميع الأسئلة] --> Continue{هل يريد الطالب متابعة الرحلة؟}
    Continue -->|لا| EndQuiz([نهاية الكويز])
    Continue -->|نعم| Exam([الانتقال إلى القسم الثالث: الامتحان الشامل])
```

## القسم الثاني: شحن رصيد النقاط

يوضح إرسال طلب الشحن، والتحقق من الإيصال ورقم العملية، ومراجعة المدير للطلب واعتماده أو رفضه.

```mermaid
flowchart TD
    StartRecharge([بدء طلب الشحن]) --> ContinueRecharge{هل يريد الطالب متابعة الشحن؟}
    ContinueRecharge -->|لا، إلغاء| CancelRecharge([العودة دون بدء الاختبار])
    ContinueRecharge -->|نعم| RechargeStart[اختيار المحفظة والمبلغ وإدخال رقم العملية]
    RechargeStart --> CheckReceipt{هل الإيصال المرفق صالح؟}
    CheckReceipt -->|لا| RechargeStart
    CheckReceipt -->|نعم أو غير مرفق| CheckDuplicate{هل رقم العملية مكرر للمحفظة نفسها؟}
    CheckDuplicate -->|نعم| RechargeStart
    CheckDuplicate -->|لا| Resubmission{هل هذا تصحيح لطلب مرفوض؟}
    Resubmission -->|نعم| UpdateRejected[تحديث السجل المرفوض نفسه إلى pending]
    Resubmission -->|لا| Pending[حفظ طلب جديد قيد المراجعة]
    UpdateRejected --> Pending
    Pending --> Telegram[إشعار تيليجرام بوصول الطلب فقط]
    Telegram --> Dashboard[المدير يسجل الدخول إلى لوحة التحكم ويراجع الطلب]
    Dashboard --> Approved{هل اعتمد المدير الطلب؟}
    Approved -->|لا| Rejected[حفظ الرفض وسببه في السجل نفسه]
    Rejected --> EditRecharge[تصحيح بيانات السجل المرفوض وإعادة إرساله]
    EditRecharge --> CheckReceipt
    Approved -->|نعم| Credit[اعتماد الطلب وإضافة نقاط الباقة]
    Credit --> Return{إلى أي اختبار يعود الطالب؟}
    Return -->|كويز درس أو وحدة| QuizBalance([العودة للتحقق من رصيد الكويز])
    Return -->|امتحان شامل| FindAttempt([العودة لطلب بدء الامتحان])
```

## القسم الثالث: الامتحان الشامل

يوضح بدء الامتحان أو استئناف محاولة نشطة، ثم الحل والتسليم والتقييم بعد انتهاء الوقت أو تأكيد التسليم.

```mermaid
flowchart TD
    ChooseExam([اختيار نموذج امتحان شامل]) --> ExamSignIn{هل الطالب مسجل؟}
    ExamSignIn -->|لا| SignIn[التسجيل أو تسجيل الدخول]
    ExamSignIn -->|نعم| FindAttempt
    SignIn --> FindAttempt[طلب بدء نموذج الامتحان]
    FindAttempt --> ExpireAttempt[إقفال محاولة بلغت ends_at بالحالة expired قبل فحص بدء جديد]
    ExpireAttempt --> Active{هل توجد محاولة in_progress للنموذج نفسه؟}
    Active -->|نعم| Resume[استئناف المحاولة دون خصم أو إنشاء جديد]
    Active -->|لا| ExamBalance{هل الرصيد يغطي points_cost؟}
    ExamBalance -->|لا| ExamRecharge{هل يريد شحن الرصيد؟}
    ExamRecharge -->|لا، إلغاء| CancelExam([العودة دون بدء الامتحان])
    ExamRecharge -->|نعم| RechargeExam([الانتقال إلى القسم الثاني: شحن الرصيد])
    ExamBalance -->|نعم| CreateAttempt[خصم points_cost وإنشاء محاولة ومؤقت الخادم]
    Resume --> LoadQuestions
    CreateAttempt --> LoadQuestions{هل حُمّلت أسئلة المحاولة؟}
    LoadQuestions -->|لا| NewAttemptCheck{هل أُنشئت الآن ولا توجد إجابات محفوظة؟}
    NewAttemptCheck -->|نعم| CancelAttempt[إلغاء المحاولة ورد النقاط]
    CancelAttempt --> LoadFailure([النهاية: إلغاء المحاولة واسترداد النقاط])
    NewAttemptCheck -->|لا، مستأنفة أو لها إجابات محفوظة| RetryLoad[عرض خطأ التحميل وإتاحة إعادة المحاولة دون إلغاء أو استرداد]
    RetryLoad --> LoadQuestions
    LoadQuestions -->|نعم| Solve[حل الأسئلة والتنقل ووضع علامات للمراجعة]
    Solve --> AnswerServer[إرسال الاختيار للخادم؛ يمكن تغييره قبل ends_at دون كشف صحته]
    AnswerServer --> Deadline{هل وصل ends_at؟}
    Deadline -->|نعم| AutoGrade[إقفال المحاولة وتقييم آخر إجابة وصلت قبله]
    Deadline -->|لا| SubmitRequest{هل طلب الطالب التسليم؟}
    SubmitRequest -->|لا| Solve
    SubmitRequest -->|نعم| Confirm{هل أكد التسليم؟}
    Confirm -->|لا| Solve
    Confirm -->|نعم| Network{هل الاتصال متاح؟}
    Network -->|لا| Wait[يبقى التسليم غير موثق ومؤقت الخادم مستمر]
    Wait --> Reconnect{هل عاد الاتصال قبل ends_at؟}
    Reconnect -->|نعم، والتسليم مؤكد| ManualGrade
    Reconnect -->|لا| Deadline
    Network -->|نعم| ManualGrade[إقفال المحاولة وتقييم آخر الإجابات المستلمة حتى التسليم]
    AutoGrade --> Result[عرض النتيجة والإجابات والتفسيرات]
    ManualGrade --> Result
    Result --> End([النهاية: نتيجة الامتحان])
```

## ملاحظات

- لا يظهر تصحيح أي كويز أو امتحان أثناء الحل؛ تظهر النتيجة والتفسيرات بعد إنهاء المحاولة.
- لا تُحفظ إجابات الكويز المفردة على الخادم؛ يحفظ الخادم ملخص محاولة الطالب المسجل فقط، وتُحفظ نتيجة الزائر محلياً.
- المؤقت الصارم وإقفال المحاولة عند `ends_at` يخصان الامتحان الشامل، وليس كويز التدريب.
