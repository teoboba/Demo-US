# Demo-US

Demo-US è un'applicazione web di annunci sviluppata come progetto di esercitazione con Laravel. Gli utenti possono pubblicare annunci; un revisore può approvarli o rifiutarli prima che compaiano nelle pagine pubbliche.

## Funzionalità

- Registrazione e accesso degli utenti.
- Creazione di annunci con titolo, prezzo, descrizione, categoria e immagini.
- Elenco degli annunci approvati, pagina di dettaglio, filtro per categoria e ricerca testuale.
- Area revisore per approvare o rifiutare gli annunci in attesa.
- Elaborazione delle immagini tramite job in coda: copertura dei volti, ridimensionamento e analisi con Google Cloud Vision. Questa parte richiede credenziali Google Cloud configurate localmente.

## Tecnologie

PHP 8.3+, Laravel 13, Blade, Livewire, Bootstrap, Vite, Laravel Fortify e Laravel Scout con TNTSearch. La configurazione di esempio usa SQLite e la coda su database.

## Avvio in locale

Servono PHP 8.3 o successivo con supporto SQLite, Composer, Node.js e npm.

```bash
git clone https://github.com/Teo-Nesich/Demo-US.git
cd Demo-US
composer install
php -r "file_exists('.env') || copy('.env.example', '.env');"
php -r "file_exists('database/database.sqlite') || touch('database/database.sqlite');"
php artisan key:generate
php artisan migrate --seed
php artisan storage:link
npm install
```

Avvia il server Laravel e Vite in due terminali separati:

```bash
php artisan serve
```

```bash
npm run dev
```

Apri l'indirizzo mostrato da `php artisan serve` (di norma `http://127.0.0.1:8000`). Il seeder crea le categorie iniziali; puoi quindi registrare un account e pubblicare un annuncio.

### Elaborazione delle immagini

Il caricamento di immagini avvia job che usano Google Cloud Vision. Per eseguirli, configura le credenziali del servizio nel file locale `google_credential.json` nella radice del progetto e avvia un worker in un altro terminale:

```bash
php artisan queue:work
```

Non inserire le credenziali nel repository. Senza questa configurazione, puoi provare il flusso degli annunci senza immagini, ma i job di elaborazione delle immagini non andranno a buon fine.

## Test

```bash
php artisan test
```

## Struttura del progetto

- `app/Livewire/CreateAnnouncement.php`: form di creazione degli annunci e caricamento immagini.
- `app/Http/Controllers/AnnouncementController.php`: elenco, dettaglio e categorie.
- `app/Http/Controllers/PublicController.php`: homepage e ricerca.
- `app/Http/Controllers/RevisorController.php`: approvazione e rifiuto degli annunci.
- `app/Jobs/`: elaborazione delle immagini in coda.
