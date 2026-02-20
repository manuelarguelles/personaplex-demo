# Proyecto: PersonaPlex Callbot

## Objetivo
Replicar el modelo de negocio de **OnBotGo** (empresa peruana de callbots/speech analytics para contact centers) pero usando tecnología superior: NVIDIA PersonaPlex en vez del stack tradicional (Dialogflow + Google STT/TTS).

## Contexto de Negocio
- **OnBotGo**: Empresa peruana (San Isidro, Lima), CEO Jorge Leiva, presencia en ~10 países LATAM + EEUU
- **Servicios que ofrecen**: Callbots (inbound/outbound), Speech Analytics, Chatbots WhatsApp/web
- **Su stack probable**: Google Dialogflow CX + Google STT/TTS + telefonía SIP + árboles de decisión
- **Certificaciones**: ISO 27001
- **Diferenciador nuestro**: PersonaPlex = full-duplex, interrupciones naturales, latencia ~200ms vs ~800-1200ms del stack tradicional

## Tecnología: NVIDIA PersonaPlex
- **Modelo**: personaplex-7b-v1 (7B parámetros)
- **Arquitectura**: Basado en Moshi (Kyutai), backbone Helium LLM 7B
- **Capacidades**: Full-duplex (escucha y habla simultáneamente), interrupciones, backchannels, voice conditioning, persona control
- **Licencia código**: MIT | **Licencia weights**: NVIDIA Open Model License (comercial OK)
- **Fecha release**: 15 enero 2026
- **Paper**: https://arxiv.org/abs/2602.06053
- **Repo oficial**: https://github.com/NVIDIA/personaplex
- **HuggingFace**: https://huggingface.co/nvidia/personaplex-7b-v1
- **Solo inglés** por ahora (entrenado en Fisher English Corpus + datos sintéticos)

### Voces disponibles
- Natural (femeninas): NATF0, NATF1, NATF2, NATF3
- Natural (masculinas): NATM0, NATM1, NATM2, NATM3
- Variedad (femeninas): VARF0-VARF4
- Variedad (masculinas): VARM0-VARM4

### Prompting
- **Asistente QA**: "You are a wise and friendly teacher..."
- **Customer Service**: Definir empresa, nombre del agente, info del servicio
- **Casual**: "You enjoy having a good conversation..."
- Soporta prompts out-of-distribution (generaliza bien)

## Hardware / Deployment

### Mac mini M4 (16GB) — nuestra máquina
- FP16 NO cabe (~14GB weights + overhead)
- **Con MLX port + cuantización 4-bit SÍ debería caber** (~4-5GB)
- Port MLX: https://github.com/mu-hashmi/personaplex-mlx (publicado 18 feb 2026)
- Comando: `python -m personaplex_mlx.local_web -q 4 --voice NATF2 --text-prompt "..."`
- Latencia esperada: mayor que A100 pero funcional para demo

### Cloud Gratis
- **Google Colab Free**: T4 16GB — justo con --cpu-offload, sesiones ~2h
- **Colab Pro** ($12/mes): A100 40-80GB — perfecto
- **Kaggle**: T4 x2, 30h/semana
- **Lightning AI**: T4/A10G free tier
- **HuggingFace Spaces**: ZeroGPU con cola

### Cloud Pago (producción)
- RunPod RTX 4090: ~$0.40/hr
- Vast.ai RTX 3090: ~$0.30/hr
- Lambda Labs A100: ~$1.10/hr

## Stack Existente (Nix Voice System)
Ya tenemos funcionando:
- ✅ Twilio (telefonía, +19808905688)
- ✅ Deepgram (STT)
- ✅ ElevenLabs (TTS)
- ✅ Servidor Node.js en localhost:8080
- ✅ ngrok para túnel público

PersonaPlex reemplazaría Deepgram + ElevenLabs + lógica de flujo en un solo modelo.

## Roadmap

### Fase 1 — Demo/Proof of Concept
- [x] Crear cuenta HuggingFace (NixAssistant) y aceptar licencia del modelo
- [x] Crear notebook Colab: `notebooks/personaplex_demo.ipynb`
- [ ] Ejecutar notebook en Google Colab con GPU T4
- [ ] Instalar personaplex-mlx en Mac mini con -q 4
- [ ] Evaluar latencia y calidad de audio
- [ ] Probar diferentes voces y personas

### HuggingFace
- **User**: NixAssistant
- **Token**: `~/.cache/huggingface/token` (Read)
- **Licencia**: Aceptada (20 Feb 2026)

### Fase 2 — Integración con Telefonía
- [ ] Conectar PersonaPlex con Twilio (SIP/WebSocket)
- [ ] Crear flujo de llamada básico (saludo → escucha → respuesta)
- [ ] Probar con llamadas reales

### Fase 3 — Motor de Flujos Configurables
- [ ] Diseñar sistema de árboles de decisión + PersonaPlex
- [ ] Panel web para crear/editar flujos
- [ ] Multi-tenant (múltiples clientes/bots)

### Fase 4 — Speech Analytics
- [ ] Guardar transcripciones de llamadas
- [ ] Análisis de sentimiento, keywords, compliance
- [ ] Dashboard de métricas

## Grupo Telegram
- **Nombre**: Test Persona Plex
- **Propósito**: Desarrollo y testing del proyecto
- **Contexto**: Este archivo (PROJECT.md) tiene todo el contexto

## Decisiones Pendientes
- ¿Cómo manejar español? (PersonaPlex solo soporta inglés)
- ¿Fine-tuning o esperar versión multilingüe?
- ¿Empezar con stack tradicional (Dialogflow) en paralelo para tener algo funcional rápido?

---
*Creado: 20 Feb 2026*
*Última actualización: 20 Feb 2026*
