# Template HTML del reporte de las 7 Maletas

Esqueleto del archivo HTML donde va el contenido de `plantilla-reporte.md`.

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Reporte 7 Maletas - [Nombre Empresa]</title>
    <link href="https://fonts.googleapis.com/css2?family=Montserrat:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body { font-family: 'Montserrat', sans-serif; line-height: 1.7; color: #232323; background: #f5f5f5; }
        .container { max-width: 1200px; margin: 0 auto; background: white; }
        .header { background: #232323; padding: 48px 80px; border-bottom: 4px solid #FAD70B; }
        .header h1 { font-size: 48px; font-weight: 700; color: #FAD70B; margin-bottom: 12px; letter-spacing: -0.5px; text-transform: uppercase; }
        .header p { font-size: 17px; color: #a1a1a6; }
        .executive-summary { padding: 64px 80px; background: #fafafa; border-bottom: 1px solid #e5e5e5; }
        .summary-title { font-size: 32px; font-weight: 700; color: #232323; margin-bottom: 32px; text-transform: uppercase; }
        .metrics { display: grid; grid-template-columns: repeat(4, 1fr); gap: 32px; margin-top: 40px; }
        .metric-value { font-size: 56px; font-weight: 700; color: #FAD70B; line-height: 1; margin-bottom: 8px; }
        .metric-label { font-size: 13px; color: #6e6e73; font-weight: 600; text-transform: uppercase; letter-spacing: 0.5px; }
        .content { padding: 80px; }
        .section { margin-bottom: 96px; }
        .section-number { font-size: 13px; color: #232323; font-weight: 700; text-transform: uppercase; letter-spacing: 1px; margin-bottom: 8px; background: #FAD70B; display: inline-block; padding: 2px 10px; }
        .section-title { font-size: 40px; font-weight: 700; color: #232323; margin-bottom: 32px; text-transform: uppercase; }
        .subsection-title { font-size: 24px; font-weight: 600; color: #232323; margin: 48px 0 20px 0; }
        p { font-size: 17px; line-height: 1.7; margin-bottom: 16px; }
        ul, ol { margin: 24px 0; padding-left: 24px; }
        li { font-size: 17px; margin: 12px 0; }
        .data-table { width: 100%; border-collapse: collapse; margin: 32px 0; border: 1px solid #e5e5e5; border-radius: 12px; overflow: hidden; }
        .data-table thead { background: #232323; }
        .data-table th { padding: 16px 20px; font-size: 13px; font-weight: 600; text-align: left; border-bottom: 1px solid #e5e5e5; color: #FAD70B; text-transform: uppercase; letter-spacing: 0.5px; }
        .data-table td { padding: 20px; font-size: 16px; border-bottom: 1px solid #e5e5e5; }
        .info-box { background: #fafafa; padding: 32px; border-radius: 12px; margin: 32px 0; border-left: 4px solid #FAD70B; }
        .quote-box { background: white; border: 1px solid #e5e5e5; padding: 28px; border-radius: 12px; margin: 24px 0; }
        .footer { background: #232323; padding: 48px 80px; text-align: center; border-top: 4px solid #FAD70B; color: #a1a1a6; }
        @media (max-width: 768px) {
            .header, .content, .executive-summary, .footer { padding: 40px 24px; }
            .metrics { grid-template-columns: 1fr 1fr; }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>Reporte 7 Maletas</h1>
            <p>[Nombre Empresa] — [Fecha]</p>
        </div>
        <div class="executive-summary">
            <h2 class="summary-title">Resumen Ejecutivo</h2>
            <p>Investigación automatizada del mercado, competidores y clientes.</p>
            <div class="metrics">
                <div class="metric"><div class="metric-value">[X]</div><div class="metric-label">Reviews Analizadas</div></div>
                <div class="metric"><div class="metric-value">[Y]</div><div class="metric-label">Competidores</div></div>
                <div class="metric"><div class="metric-value">[Z]</div><div class="metric-label">Fuentes Consultadas</div></div>
                <div class="metric"><div class="metric-value">7</div><div class="metric-label">Maletas Completadas</div></div>
            </div>
        </div>
        <div class="content">
            <!-- Aquí va el contenido de las 7 maletas con el formato adecuado -->
            <!-- Usa <div class="section"> para cada maleta -->
            <!-- Usa <div class="info-box"> para destacar puntos clave -->
            <!-- Usa <div class="quote-box"> para evidencias/citas -->
        </div>
        <div class="footer">
            <p>Reporte generado por el sistema 7 Maletas</p>
        </div>
    </div>
</body>
</html>
```
