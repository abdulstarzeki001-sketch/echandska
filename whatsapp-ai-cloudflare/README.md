# WhatsApp AI Assistant — Cloudflare Workers

مشروع رسمي لربط **WhatsApp Business Platform (Cloud API)** مع **OpenAI API** وتشغيله على **Cloudflare Workers**.

## ماذا يفعل؟

- يستقبل Webhook الرسمي من Meta.
- يتحقق من `META_VERIFY_TOKEN`.
- يتحقق من توقيع `X-Hub-Signature-256` باستخدام `META_APP_SECRET`.
- يرسل نص العميل إلى OpenAI Responses API.
- يعيد الرد إلى العميل عبر WhatsApp Cloud API.
- يدعم التحويل اليدوي عند كتابة كلمات مثل: `موظف` أو `human`.
- لا يضع أي API keys داخل GitHub.
- لا يحتاج قاعدة بيانات في النسخة الأولى، لذلك النشر مباشر.

## 1) نشر المشروع على Cloudflare

في Cloudflare Dashboard:

1. افتح **Workers & Pages**.
2. أنشئ Worker من GitHub.
3. اختر المستودع:
   `abdulstarzeki001-sketch/echandska`
4. اختر الفرع:
   `whatsapp-ai-cloudflare`
5. اجعل **Root directory**:
   `whatsapp-ai-cloudflare`
6. Deploy command:
   `npx wrangler deploy`

يمكن أيضاً النشر من الطرفية:

```bash
npm install
npx wrangler deploy
```

## 2) الأسرار المطلوبة في Cloudflare

من Worker > Settings > Variables and Secrets أضف:

### Secrets

- `OPENAI_API_KEY`
- `WHATSAPP_TOKEN`
- `WHATSAPP_PHONE_NUMBER_ID`
- `META_VERIFY_TOKEN`
- `META_APP_SECRET`

### Variables اختيارية

- `OPENAI_MODEL` — الافتراضي: `gpt-5-mini`
- `META_GRAPH_VERSION` — الافتراضي: `v24.0`
- `BOT_SYSTEM_PROMPT` — تعليمات المساعد.

لا تضع الأسرار داخل `wrangler.jsonc` أو GitHub.

## 3) إعداد Meta / WhatsApp

في Meta for Developers:

1. أنشئ تطبيق Business.
2. أضف منتج WhatsApp.
3. من WhatsApp > API Setup خذ:
   - Phone Number ID
   - Access Token
4. من App Settings > Basic خذ **App Secret**.
5. اختر نصاً سرياً طويلاً ليكون `META_VERIFY_TOKEN`.
6. بعد نشر Worker سيكون الرابط تقريباً:

```
https://whatsapp-ai-assistant.<your-subdomain>.workers.dev/webhook
```

ضعه كـ **Callback URL** في إعداد Webhooks داخل Meta، وضع نفس `META_VERIFY_TOKEN` في Verify Token.

بعد نجاح التحقق اشترك في حقل:

`messages`

## 4) فحص Worker

افتح:

```
https://whatsapp-ai-assistant.<your-subdomain>.workers.dev/health
```

المفروض يرجع:

```json
{
  "ok": true,
  "service": "whatsapp-ai-cloudflare",
  "webhook": "/webhook"
}
```

## 5) للإنتاج

استخدم Access Token دائم مناسب للإنتاج، ولا تعتمد على Temporary Token الخاص بصفحة التجربة.

## 6) تخصيص شخصية المساعد

غيّر `BOT_SYSTEM_PROMPT` من Cloudflare بدون تعديل الكود. مثال:

```
أنت مساعد شركة نقل. أجب بالعربية العراقية باختصار. لا تخترع أسعاراً.
إذا لم تكن المعلومة موجودة فقل إن موظف الشركة سيؤكدها.
```

## ملاحظة

هذه النسخة تتعامل مع الرسائل النصية. يمكن إضافة الصور، الصوت، PDF، قاعدة D1، سجل العملاء، لوحة إدارة، وربط النظام المحاسبي لاحقاً.
