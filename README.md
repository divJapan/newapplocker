# App Locker

App Android che blocca con impronta digitale (o PIN/pattern di sistema) le app scelte dall'utente, usando un `AccessibilityService` per rilevare i cambi di app in primo piano.

## Come aprire il progetto

1. Apri Android Studio → **Open** → seleziona la cartella `AppLocker`.
2. Lascia sincronizzare Gradle (la prima volta scaricherà le dipendenze).
3. Collega un device fisico o avvia un emulatore con **Android 8.0 (API 26)** o superiore.
4. Premi **Run**.

## Prima configurazione nell'app

1. Al primo avvio, tocca **"Attiva servizio di accessibilità"** → ti porta nelle Impostazioni di sistema → cerca "App Locker" nell'elenco dei servizi di accessibilità → attivalo.
2. Tocca **"Consenti sovrapposizione ad altre app"** → concedi il permesso (necessario per mostrare la schermata di blocco sopra le altre app).
3. Nella lista, attiva lo switch sulle app che vuoi proteggere.
4. Apri una delle app selezionate: dopo un istante comparirà la richiesta di impronta/PIN.

## Note tecniche

- **Perché serve l'Accessibility Service**: da Android 10+ non esiste più un modo pubblico per rilevare "app in primo piano" senza permessi speciali. `UsageStatsManager` è un'alternativa ma è meno reattivo (polling, ritardi di qualche secondo). L'Accessibility Service riceve l'evento quasi istantaneamente.
- **Google Play**: se pubblichi quest'app su Play Store, l'uso dell'Accessibility Service per uno scopo diverso dall'accessibilità reale richiede una dichiarazione specifica nella Play Console e Google può rifiutarla. Per un uso personale/sideload non è un problema.
- **Timeout di sblocco**: dopo un'autenticazione riuscita, l'app resta "sbloccata" per 5 secondi (`Prefs.DEFAULT_TIMEOUT_MS`) per evitare richieste ripetute passando tra le schermate della stessa app. Puoi modificare questo valore in `Prefs.kt`.
- **Limiti noti**: alcuni produttori (Xiaomi, Huawei, Oppo...) limitano aggressivamente i servizi in background; potrebbe essere necessario disattivare l'ottimizzazione batteria per l'app nelle impostazioni del produttore.

## Struttura del progetto

- `MainActivity.kt` — lista delle app installate con switch per selezionare quelle da proteggere.
- `LockAccessibilityService.kt` — rileva il cambio di app in primo piano.
- `LockActivity.kt` — mostra `BiometricPrompt` (impronta + fallback PIN/pattern di sistema).
- `Prefs.kt` — persistenza della lista app protette tramite `SharedPreferences`.
- `AppListAdapter.kt` — adapter RecyclerView per la lista.
