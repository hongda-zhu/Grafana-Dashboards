# Repositori de Taulers de Control de Grafana per a WellTrack

Aquest repositori emmagatzema les definicions dels taulers de control de Grafana en format JSON per al projecte WellTrack.

## Propòsit

L'objectiu principal d'aquest repositori és gestionar de forma centralitzada tots els taulers de control personalitzats de Grafana utilitzats per monitoritzar l'aplicació WellTrack i la seva infraestructura subjacent. En versionar aquests taulers de control amb Git, assegurem:

*   **Versionat i Historial:** Seguiment dels canvis en els taulers de control al llarg del temps.
*   **Col·laboració:** Permet que diversos membres de l'equip contribueixin i revisin els taulers de control.
*   **Automatització (GitOps):** Els canvis en aquest repositori poden activar automàticament l'actualització dels taulers de control en l'entorn de Grafana desplegat, típicament mitjançant Argo CD.

## Com Funciona

1.  **Emmagatzematge de JSON:** Cada tauler de control es desa com un fitxer `.json` individual en aquest repositori (per exemple, `falco-logs.json`, `falco-metrics.json`).
2.  **Creació de ConfigMap:** Un procés automatitzat, gestionat per una aplicació Argo CD dedicada (definida, per exemple, a `PTI-WellTrackDevops/bootstrap/templates/grafana-dashboards-app.yaml`), recull aquests fitxers JSON. Aquesta aplicació Argo CD transforma cada fitxer JSON en un ConfigMap de Kubernetes dins del clúster.
3.  **Descobriment per Grafana:** La instància de Grafana desplegada (part de la pila de monitorització, configurada a `PTI-WellTrackDevops/bootstrap/values/monitoring.yaml`) està configurada per descobrir i carregar automàticament taulers de control des d'aquests ConfigMaps. Això es fa normalment mitjançant l'etiquetatge adequat dels ConfigMaps i la configuració d'un proveïdor de taulers de control a Grafana que monitoritza aquestes etiquetes.

## Taulers de Control Actuals

Actualment, aquest repositori inclou els següents taulers de control:

*   `falco-logs.json`: Un tauler de control per visualitzar els registres de Falco, proporcionant informació sobre esdeveniments de seguretat en temps d'execució.
*   `falco-metrics.json`: Un tauler de control per visualitzar mètriques relacionades amb Falco, oferint una visió general del rendiment i l'activitat de Falco.

## Com Afegir Nous Taulers de Control

Per afegir un nou tauler de control de Grafana a aquest sistema:

1.  **Exporta el Tauler de Control:**
    *   Des de la interfície d'usuari de Grafana, navega fins al tauler de control que vols afegir.
    *   Fes clic a la icona de "Share dashboard".
    *   Ves a la pestanya "Export".
    *   Activa l'opció "Export for sharing externally" (Exportar per compartir externament). Això ajuda a parametritzar les fonts de dades (datasources), fent que el tauler de control sigui més portable.
    *   Fes clic a "Save to file" per descarregar el tauler de control com un fitxer JSON.

2.  **Afegeix el JSON al Repositori:**
    *   Col·loca el fitxer JSON del nou tauler de control en aquest repositori (`Grafana-Dashboards`).
    *   Si el nombre de taulers de control creix, considera organitzar-los en subdirectoris per a una millor gestió.

3.  **Commit i Push:**
    *   Fes `git add` amb el nou fitxer JSON.
    *   Fes `git commit` amb un missatge descriptiu.
    *   Fes `git push` dels canvis al repositori remot.

4.  **Sincronització Automàtica:**
    *   Si el flux de treball GitOps amb Argo CD està correctament configurat, Argo CD detectarà els canvis en aquest repositori.
    *   L'aplicació Argo CD responsable dels taulers de control (per exemple, `grafana-dashboards-app`) sincronitzarà els canvis, creant o actualitzant el ConfigMap corresponent al clúster de Kubernetes.
    *   Poc després, el nou tauler de control hauria d'aparèixer automàticament a la interfície d'usuari de Grafana.
