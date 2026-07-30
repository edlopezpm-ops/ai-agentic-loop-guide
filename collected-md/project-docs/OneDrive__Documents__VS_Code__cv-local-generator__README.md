# CV Local Generator

App local para evaluar cargos contra un CV master, adaptar un CV segun palabras clave, y generar CV + cover letter en PDF.

## Objetivo del proyecto

La idea es tener un flujo simple:

1. Guardas tus documentos master en `core/`.
2. Pegas una descripcion de cargo o guardas archivos en `jobs/`.
3. La app compara el cargo contra el core CV y core experience.
4. Recibes un assessment de probabilidad relativa para aplicar.
5. Decides si procede generar el paquete.
6. La app genera CV + cover letter en PDF.

La app no inventa empresas, fechas, cargos ni logros. Solo prioriza y redacta usando informacion de los documentos core.

## Documentos master

Los archivos master viven en:

```text
core/core-cv.txt
core/core-experience.txt
```

Para cambiar la experiencia base, reemplaza esos archivos con nuevos documentos usando los mismos nombres. Luego reinicia o refresca la app.

`core-cv.txt` debe contener el CV master limpio.

`core-experience.txt` puede contener notas largas, historia profesional, detalles tecnicos, aprendizajes y contexto que ayuden a redactar mejor el CV y la cover letter.

## Version Python recomendada

Instala dependencias:

```powershell
py -m pip install -r requirements.txt
```

Desde la carpeta raiz `Py`, puedes ejecutar la app de escritorio con un solo comando:

```powershell
.\Run-CVGenerator.ps1
```

Si estas dentro de `cv-local-generator`, usa:

```powershell
.\Run-CVGenerator.ps1
```

Para validar sin abrir la ventana:

```powershell
.\Run-CVGenerator.ps1 -Check
```

Para abrir la version navegador:

```powershell
.\Run-CVGenerator.ps1 -Web
```

Tambien puedes ejecutar la app web directamente:

```powershell
py cv_app.py
```

Abre en el navegador:

```text
http://127.0.0.1:8765
```

## Version escritorio

Tambien puedes usar una UI simple de escritorio:

```powershell
py desktop_app.py
```

La ventana guia el flujo:

1. Confirmar los archivos master en `core/`.
2. Cargar o pegar la descripcion del cargo.
3. Evaluar el cargo.
4. Generar CV + cover letter.
5. Abrir `output/pdf/`.

Los PDFs se guardan en:

```text
output/pdf/
```

## Flujo con AI sin API paga

La app incluye un prompt listo para copiar en ChatGPT manualmente. Esto permite que una persona use una cuenta ChatGPT Free como apoyo, sin integrar llamadas automaticas a la API.

No se agrego integracion directa con OpenAI API porque requiere `OPENAI_API_KEY` y el uso de API se factura por consumo. Para mantener el proyecto open source y accesible, la version actual funciona localmente sin cobros por token.

## Crear instalador / EXE para Windows

La forma mas simple es crear una carpeta distribuible con un `.exe`:

```powershell
.\build_windows_exe.ps1
```

El resultado queda en:

```text
dist/CVLocalGenerator/CVLocalGenerator.exe
```

Distribuye la carpeta completa `dist/CVLocalGenerator`, no solo el `.exe`, porque ahi viven `core/`, `jobs/` y `output/`.

Para una experiencia tipo instalador, puedes comprimir esa carpeta en `.zip`. Mas adelante se puede agregar Inno Setup o WiX para generar un instalador `.msi` o `.exe` con accesos directos.

## Cargos soportados

Coloca descripciones de cargo en `jobs/` con estos formatos:

- `.txt`
- `.md`
- `.csv`
- `.html`
- `.htm`
- `.docx`

Los archivos `.doc` antiguos no se leen sin Microsoft Word. Guardalos como `.docx` o `.txt`.

## Version HTML simple

Abre este archivo en tu navegador:

`app.html`

No necesitas instalar nada para esa version, pero el PDF se hace desde la ventana de impresion del navegador.

## Archivos importantes

- `app.html`: toda la app local: estructura, estilos y JavaScript.
- `cv_app.py`: app Python local con lectura de cargos, adaptacion por keywords y PDF real.
- `desktop_app.py`: UI de escritorio con pasos guiados.
- `build_windows_exe.ps1`: build de Windows con PyInstaller.
- `core/core-cv.txt`: CV master usado como base.
- `core/core-experience.txt`: experiencia master usada para assessment y cover letter.
- `data/sample-cv.json`: ejemplo de datos que la app puede importar.
- `jobs/sample-job.txt`: ejemplo de cargo para probar la adaptacion.
- `docs/ai-json-prompt.md`: prompt guia para pedirle a una AI que genere JSON compatible.

## Formato JSON esperado

```json
{
  "profile": {
    "name": "Tu Nombre",
    "title": "Cargo objetivo",
    "location": "Ciudad, Pais",
    "phone": "+1 000 000 0000",
    "email": "correo@email.com",
    "linkedin": "linkedin.com/in/usuario",
    "summary": "Resumen profesional..."
  },
  "skills": ["Skill 1", "Skill 2"],
  "experience": [
    {
      "role": "Cargo",
      "company": "Empresa",
      "period": "2022 - Presente",
      "bullets": ["Logro 1", "Logro 2"]
    }
  ],
  "education": [
    {
      "degree": "Titulo",
      "institution": "Institucion",
      "period": "2018 - 2022"
    }
  ]
}
```

## Siguiente etapa recomendada

Cuando quieras mejorarlo, el siguiente paso natural es agregar una opcion de AI local/API para reescritura mas fina. La base actual ya deja el JSON editable para revisar cada cambio antes de exportar.
