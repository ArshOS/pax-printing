# POS Ticket Printing in Android (Jetpack Compose + PAX Device)

This project demonstrates how to generate and print individual ticket bitmaps on a POS device (e.g., PAX A920/A930) using Jetpack Compose in a native Android application. The flow covers rendering ticket information, generating bitmaps (including QR codes), and sending them one-by-one to the printer while handling memory efficiently.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Ticket Printing Flow](#ticket-printing-flow)
- [Jetpack Compose UI](#jetpack-compose-ui)
- [Bitmap Generation](#bitmap-generation)
- [Printing on PAX Device](#printing-on-pax-device)
- [Memory Management](#memory-management)
- [Best Practices](#best-practices)
- [Future Improvements](#future-improvements)

---

## 🧭 Overview

The system is designed to handle ticket printing on POS machines without combining all tickets into one long image. Instead, each ticket is generated, processed, and printed individually. This approach prevents `OutOfMemoryError` issues, especially when printing dozens or hundreds of tickets.

---

## 🎟️ Ticket Printing Flow

1. Receive ticket data from the backend in JSON format.
2. Deserialize the data and generate individual ticket bitmaps.
3. Print each bitmap using the PAX device's built-in printer.
4. Add footer bitmaps if needed.
5. Recycle bitmaps after printing to release memory.

---

## 🖼️ Jetpack Compose UI

The `PaymentSuccessScreen` displays ticket confirmation and provides a button to print tickets.

```kotlin
@Composable
fun PaymentSuccessScreen(
    data: UpdatePaymentResponse,
    transactionIdRZP: String
) {
    val context = LocalContext.current
    val coroutineScope = rememberCoroutineScope()

    Button(onClick = {
        coroutineScope.launch {
            val tickets = withContext(Dispatchers.Default) {
                generateTicketBitmapsFromJson(context, Gson().toJson(data))
            }

            tickets.forEach { bitmapPair ->
                printBitmap(bitmapPair.second, bitmapPair.second, context)
                delay(3000L)
                bitmapPair.second.recycle() // Recycle after use
            }
        }
    }) {
        Text("Print Tickets")
    }
}
```

---

## 🖌️ Bitmap Generation

You generate the ticket and QR bitmaps in the `generateTicketBitmapsFromJson` function.

```kotlin
fun generateTicketBitmapsFromJson(context: Context, json: String): List<Pair<String, Bitmap>> {
    val result = mutableListOf<Pair<String, Bitmap>>()
    // Deserialize ticket info and draw each bitmap on canvas
    // For each bitmap:
    // - Create QR code
    // - Compose layout
    // - Draw on Canvas
    // - Return (ticketId, Bitmap)
    return result
}
```
---

## 🖨️ Printing on PAX Device

Use the PAX printer SDK to send each bitmap to the printer.

```kotlin
fun printBitmap(original: Bitmap, scaled: Bitmap, context: Context) {
    val printer = PaxPrinterHelper.getInstance()
    printer.init()
    printer.printBitmap(scaled)
    printer.start()
}
```
---

## 🧠 Memory Management

Avoid combining all ticket bitmaps into one image. Instead:

1. Generate and print one bitmap at a time.
2. Recycle bitmaps manually using `bitmap.recycle()` after printing.
3. Add delays between prints to prevent overloading the print queue.
4. Always handle printing on `Dispatchers.Default` to keep UI responsive.

## Example
```kotlin
tickets.forEach { bitmapPair ->
    printBitmap(bitmapPair.second, bitmapPair.second, context)
    delay(3000L)
    bitmapPair.second.recycle()
}
```
---

## ✅ Best Practices

- Offload heavy bitmap logic to background threads using withContext(Dispatchers.Default).
- Use LazyColumn only for displaying previews, not for printing.
- Compress bitmaps before printing to save memory.
- Always test on real POS hardware for performance.

---

## 🚀 Future Improvements

- Add multi-language support for ticket content.
- Support dark mode ticket styling.
- Cache and re-use footers instead of generating them every time.
- port ticket images as PDFs for digital receipts.

---

## 🤝 Contributions

Feel free to fork, submit PRs, or raise issues. This is designed to help POS developers build efficient, memory-safe printing workflows on native Android with Jetpack Compose.

    

    

    

    
    

    

    

    

