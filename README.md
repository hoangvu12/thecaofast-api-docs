# **HƯỚNG DẪN TÍCH HỢP API**

## 1. Lấy API Key

Đăng nhập vào thecaofast: https://thecaofast.vercel.app/login
Truy cập vào: https://thecaofast.vercel.app/dashboard/api

![Lấy API KEY](https://i.ibb.co/MRN9nfS/image.png)

## 2. Tích hợp API

Có 2 bước để thực hiên việc gạch thẻ:

**B1**. Gửi thông tin thẻ cần gạch sang thecaofast thông qua API.
**B2**. Nhận kết quả gạch thẻ thông qua 1 trong 2 cách:

1.  Sử dụng API của thecaofast để lấy kết quả của thẻ thông qua orderId.
2.  Sử dụng webhook (callback) để thecaofast chủ động gửi kết quả của thẻ cho bạn.

### 1. API

**1.1, Gửi thẻ**
URL: `https://thecaofast.vercel.app/api/v1/orders`
Method: `POST`
Body:

```json
{
    "secretKey": String,
    "cardNumber": String,
    "cardSerial": String,
    "cardType": String,
    "cardAmount": Number,
    "callbackUrl": String (Optional)
}
```

_Chỉ dùng callbackUrl khi muốn nhận kết quả thẻ qua callback_
_Loại thẻ cào hỗ trợ: viettel, mobifone, vinaphone, vtc, garena, zing, gate_

## Success Response

**Code** : `200 OK`

**Kết quả trả về**

```json
{
    "success": true,
    "response": {
		"success": Boolean,
		"message:" String,
		"orderId" Id
	}
}
```

**Ví dụ**
_Gửi thẻ thành công_

```json
{
    "success": true,
    "response": {
		"success": true,
		"message:" "Gửi thẻ thành công",
		"orderId" 493107385291
	}
}
```

_Gửi thẻ thất bại_

```json
{
    "success": true,
    "response": {
		"success": false,
		"message:" "Gửi thẻ thất bại",
		"orderId" 493107385291
	}
}
```

## Error Response

**Code** : `400 BAD REQUEST`

**Kết quả trả về**

```json
{
    "success": false,
    "error": String,
    "errorMessage": String
}
```

**Ví dụ**

```json
{
  "success": false,
  "error": "Something went wrong",
  "errorMessage": "Lỗi hệ thống, vui lòng thử lại"
}
```

**1.2, Lấy kết quả gạch thẻ**

_1.2.1, Truy vấn trạng thái gạch thẻ qua HTTP GET_

URL: `https://thecaofast.vercel.app/api/v1/orders/:orderId`
Method: `GET`

## Success Response

**Code** : `200 OK`

**Kết quả trả về**

```json
{
    "success": true,
    "response": {
		"success": Boolean,
		"message:" String,
		"status": String,
		"cardAmount": Number
	}
}
```

**Ví dụ**
_Gạch thẻ thành công_

```json
{
    "success": true,
    "response": {
		"success": true,
		"message:" "Gạch thẻ thành công",
		"status": "success",
		"cardAmount": 10000
	}
}
```

_Gạch thẻ thất bại_

```json
{
    "success": true,
    "response": {
		"success": false,
		"message:" "Gạch thẻ thất bại",
		"status": "failed",
		"cardAmount": 10000
	}
}
```

_Đang xử lý_

```json
{
    "success": true,
    "response": {
		"success": false,
		"message:" "Hệ thống đang xử lí",
		"status": "pending",
		"cardAmount": 10000
	}
}
```

## Error Response

**Code** : `400 BAD REQUEST`

**Kết quả trả về**

```json
{
    "success": false,
    "error": String,
    "errorMessage": String
}
```

**Ví dụ**

```json
{
  "success": false,
  "error": "Something went wrong",
  "errorMessage": "Lỗi hệ thống, vui lòng thử lại"
}
```

_1.2.2, Truy vấn trạng thái gạch thẻ qua HTTP GET_

Khi thẻ đã có kết quả, thecaofast sẽ gửi thông tin qua method POST cho callbackUrl bạn đã cung cấp khi gửi thẻ

```json
{
    "orderId": Id,
    "cardNumber": String,
    "cardSerial": String,
    "cardAmount" Number,
    "success": Boolean
}
```

_Hãy chắc chắn rằng callbackUrl của bạn chấp nhận POST method_
