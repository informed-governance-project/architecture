# Architecture

The platform is built around a layered architecture that guides how information moves from users to the system and back. When someone accesses the platform through their browser, their requests first pass through a secure web gateway that manages entry and ensures stable, encrypted communication. These requests are then routed to the application core, where the main governance, incident management, reporting, and security objective processes are executed. The system separates immediate user interactions from heavier operations: while simple actions are handled instantly by the application, more complex or scheduled tasks—such as generating reports or sending notifications—are processed in the background by a dedicated task engine. All data is stored securely in a central database, with fast caching and messaging components ensuring the platform remains responsive even under heavy use. The application then uses external email services to deliver notifications. This architecture makes the platform reliable, scalable, and efficient, ensuring that users experience smooth interactions while the system processes complex operations behind the scenes.

## Current architecture
``` mermaid
---
config:
  layout: elk
---
flowchart TB
 subgraph subGraph0["Client Zone"]
        Browser["Web Browser"]
        PDF["PDF NI & SO"]
  end
 subgraph subGraph1["Web Server"]
        WebServer["NGINX/Apache"]
  end
 subgraph subGraph2["Proxy Zone"]
        ServerProxy["Gunicorn"]
        ServerStatic["Whitenoise"]
  end
 subgraph subGraph3["BackEnd Zone"]
        WSGI["WSGI Entry Point"]
        CoreSettings["Settings & URLs"]
        GovernanceApp["Governance Module"]
        IncidentsApp["Incidents Module"]
        ReportingApp["Reporting Module"]
        SOApp["Security Objectives Module"]
  end
 subgraph subGraph4["Async Tasks Zone"]
        CeleryWorker["Celery Worker"]
        CeleryBeat["Celery Beat Scheduler"]
        GPTasks["Governance Tasks"]
        IncTasks["Incidents Tasks"]
        RepTasks["Reporting Tasks"]
  end
 subgraph subGraph5["Theme Zone"]
        Static["Static Assets"]
        Templates["HTML Templates"]
  end
 subgraph subGraph6["Data Zone"]
        Database["Database"]
        Broker["Redis Broker"]
        PDFModule["PDF Generation Module"]
  end
 subgraph subGraph7["External Zone"]
        SMTP["Email SMTP Server"]
  end
    Browser -- HTTP(S) --> WebServer
    WebServer -- execute --> ServerProxy
    CoreSettings -- serves --> Templates
    ServerProxy -- proxies HTTP --> WSGI
    ServerStatic -- loads --> Static
    WSGI -- loads --> CoreSettings
    CoreSettings -- initializes --> GovernanceApp & IncidentsApp & ReportingApp & SOApp
    GovernanceApp -- SQL --> Database
    IncidentsApp -- SQL --> Database
    ReportingApp -- SQL --> Database
    SOApp -- SQL --> Database
    CeleryBeat -- schedule AMQP --> Broker
    CeleryWorker -- consume AMQP --> Broker
    CeleryWorker -- execute --> GPTasks & IncTasks & RepTasks
    IncTasks -- sends --> SMTP
    RepTasks -- generate --> PDFModule
    IncidentsApp -- sends --> SMTP
     Browser:::ui
     PDF:::ui
     WebServer:::web
     ServerProxy:::backend
     ServerStatic:::backend
     WSGI:::backend
     CoreSettings:::backend
     GovernanceApp:::backend
     IncidentsApp:::backend
     ReportingApp:::backend
     SOApp:::backend
     CeleryWorker:::worker
     CeleryBeat:::worker
     GPTasks:::worker
     IncTasks:::worker
     RepTasks:::worker
     Static:::ui
     Templates:::ui
     Database:::infra
     Broker:::infra
     PDFModule:::infra
     SMTP:::external
    classDef web fill:#F7C0C0,stroke:#333,stroke-width:1px
    classDef ui fill:#bbdefb,stroke:#333,stroke-width:1px
    classDef backend fill:#c8e6c9,stroke:#333,stroke-width:1px
    classDef worker fill:#ffe0b2,stroke:#333,stroke-width:1px
    classDef infra fill:#e0e0e0,stroke:#333,stroke-width:1px
    classDef external fill:#e1bee7,stroke:#333,stroke-width:1px

```





