# Universal Print Tray

`UniversalPrint.Tray` is a Windows tray application for local direct PDF printing from the cashier web project. It exposes a small HTTP API on `http://127.0.0.1:5000/` and routes logical printer slots like `Printer01` to real Windows printers.

## What It Does

- Runs in the Windows system tray.
- Starts a local self-hosted Web API.
- Prevents duplicate tray instances from running.
- Lets client apps print PDF jobs through fixed logical slots:
  - `Printer01`
  - `Printer02`
  - `Printer03`
  - `Printer04`
  - `Printer05`
- Prints PDF files internally without depending on Adobe Reader or a browser PDF handler.

## Requirements

- Windows
- .NET Framework 4.8
- One or more installed Windows printers

## Run The App

Build and run from the project folder:

```powershell
dotnet build UniversalPrintService.sln
dotnet run --project UniversalPrintService.csproj
```

After startup, the tray icon appears and the local API listens on:

```text
http://127.0.0.1:5000/
```

## Tray Menu

The tray app provides:

- `Status`
- `Printers`
- `Queue`
- `Logs`
- `Startup`
- `Settings`
- `About`
- `Exit`

Use `Settings` to map `Printer01` to `Printer05` to real installed printers.

## API Endpoints

- `GET /health`
- `GET /printers`
- `GET /printer-slots`
- `POST /printer-slots`
- `GET /jobs`
- `POST /print`

## Print Request Format

All print requests are sent to:

```text
POST http://127.0.0.1:5000/print
```

The direct-print client accepts only PDF jobs. `type` may be set to `pdf`, or left blank and it will be treated as `pdf`.

### PDF Print With Absolute Path

```json
{
  "printer": "Printer01",
  "type": "pdf",
  "copies": 1,
  "content": "C:\\temp\\receipt_1001.pdf"
}
```

### PDF Print With Base64

```json
{
  "printer": "Printer01",
  "type": "pdf",
  "copies": 1,
  "content": "JVBERi0xLjQKJ..."
}
```

## Notes About PDF Printing

- PDF printing is handled internally through an embedded PDF renderer.
- `content` may be:
  - an absolute `.pdf` file path
  - a raw base64 PDF string
  - a PDF data URL beginning with `data:application/pdf;base64,`
- PDF jobs preserve the layout already present in the PDF file.
- PDF jobs do not require the target printer to be the Windows default printer.

## Printer Slot Storage

Slot mappings are stored in:

```text
Data\printer-slots.json
```

User preferences and logs are stored under:

```text
Data\
Logs\
```
