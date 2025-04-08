# Recursos y Plataformas de Práctica para Análisis de Phishing

La teoría y los workflows son importantes, pero la **práctica constante** es clave para volverse realmente competente en el análisis de phishing. Aquí tienes algunos recursos donde puedes encontrar ejemplos, practicar y aprender más:

## 💻 Plataformas de Práctica (Hands-On Labs)

Estas plataformas ofrecen escenarios simulados donde puedes analizar correos de phishing en un entorno seguro:

* **[LetsDefend](https://letsdefend.io/)**:
    * Ofrece un módulo específico de "Phishing Analysis" donde recibes correos simulados y debes analizarlos usando herramientas virtuales. Muy práctico y enfocado. (Tiene planes gratuitos y de pago).

* **[Blue Team Labs Online (BTLO)](https://blueteamlabs.online/)**:
    * Plataforma hermana de la certificación BTL1. Frecuentemente incluye desafíos (Challenges) gratuitos y de pago que involucran análisis de phishing como parte de investigaciones más amplias (DFIR, SIEM).

* **[CyberDefenders](https://cyberdefenders.org/)**:
    * Plataforma con desafíos estilo CTF enfocados en Blue Team y DFIR. Muchos de sus retos comienzan con un vector de acceso inicial como el phishing, requiriendo análisis de correos, URLs o adjuntos.

* **[TryHackMe](https://tryhackme.com/)**:
    * Aunque más amplia, tiene salas (rooms) y rutas (paths) enfocadas en SOC y análisis de seguridad que pueden incluir módulos o tareas relacionadas con el análisis de phishing.

## 🎣 Fuentes de Muestras y Datos (URLs/Emails)

Estos sitios listan URLs reportadas como phishing. **⚠️ ¡EXTREMA PRECAUCIÓN!** No visites estas URLs directamente en tu máquina principal. Úsalas para investigar en servicios de reputación (VT, URLScan) o en entornos muy controlados.

* **[PhishTank](https://phishtank.org/)**:
    * Una base de datos colaborativa donde los usuarios reportan y verifican sitios de phishing. Puedes explorar URLs reportadas.

* **[OpenPhish](https://openphish.com/)**:
    * Proporciona feeds (listas) de URLs de phishing activas. Útil para inteligencia, pero maneja los datos con cuidado extremo.

* **Búsqueda en GitHub/Internet:**
    * A veces puedes encontrar repositorios o blogs con colecciones de emails `.eml` de ejemplo para análisis offline. Busca términos como `"phishing email samples"`, `"eml phishing examples"`. **Verifica siempre la fuente y maneja estos archivos con las mismas precauciones que un adjunto real.**

## 📰 Blogs y Aprendizaje Continuo

Mantente al día con las últimas técnicas y campañas de phishing siguiendo blogs de empresas de seguridad y analistas:

* **[Cofense Blog](https://cofense.com/blog/)**: Especializados en defensa contra phishing.
* **[Proofpoint Threat Insight](https://www.proofpoint.com/us/blog/threat-insight)**: Investigación sobre amenazas, incluyendo phishing.
* **[KnowBe4 Blog](https://blog.knowbe4.com/)**: Enfocados en concienciación, pero con análisis técnicos frecuentes.
* **[SANS Internet Storm Center (ISC)](https://isc.sans.edu/)**: Informes diarios sobre amenazas, a menudo incluyen análisis de campañas de phishing.
* **Búsquedas Específicas:** Busca en Google o Twitter análisis de campañas recientes (ej. `"Qakbot phishing analysis"`, `"IcedID email analysis"`).

## 🛠️ Documentación de Herramientas

No olvides consultar la documentación oficial de las herramientas mencionadas en `02_Herramientas.md`. Suelen contener guías detalladas, ejemplos y explicaciones de todas sus funcionalidades:

* Documentación de VirusTotal, URLScan.io, Any.Run, Hybrid Analysis, MxToolbox, etc.

---
