# Prompt para generar JSON compatible

Copia este prompt y adaptalo con tu CV base y la descripcion del cargo.

```text
Actua como un asistente experto en CVs y aplicaciones laborales.

Voy a darte:
1. Mi CV base.
2. La descripcion del cargo al que quiero aplicar.

Tu tarea:
- Adaptar mi perfil profesional al cargo.
- Mantener la informacion honesta y basada en mi experiencia real.
- Mejorar claridad, impacto y palabras clave.
- No inventar empresas, fechas, titulos ni logros que no esten respaldados.
- Devolver solamente JSON valido, sin Markdown y sin explicaciones.

El JSON debe seguir exactamente esta estructura:

{
  "profile": {
    "name": "",
    "title": "",
    "location": "",
    "phone": "",
    "email": "",
    "linkedin": "",
    "summary": ""
  },
  "skills": [],
  "experience": [
    {
      "role": "",
      "company": "",
      "period": "",
      "bullets": []
    }
  ],
  "education": [
    {
      "degree": "",
      "institution": "",
      "period": ""
    }
  ]
}

Mi CV base:
[PEGA AQUI TU CV]

Descripcion del cargo:
[PEGA AQUI EL JOB POSTING]
```
