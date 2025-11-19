# Laravel Super Kit

A production-ready Laravel 12 starter kit focused on speed, security, and developer experience. It ships with robust authentication (2FA included), a clean Livewire + Blade UI powered by Flux, Tailwind CSS 4 with Vite, and battle-tested tooling (Pest, PHPStan, Rector, Pint).

---

## 📋 Prerequisites

Before you begin, ensure your development environment meets the following requirements. This kit relies on modern PHP and Node.js versions to deliver the best performance and developer experience.

| Package      | Version      | Description                                                                |
| :----------- | :----------- | :------------------------------------------------------------------------- |
| **PHP**      | `^8.4`       | The latest stable version of PHP is required for modern language features. |
| **Laravel**  | `^12.0`      | The framework core.                                                        |
| **Node.js**  | `≥ 18`       | Required for building frontend assets with Vite.                           |
| **PNPM**     | `≥ 8`        | The preferred package manager for Node dependencies (`npm i -g pnpm`).     |
| **Composer** | `≥ 2`        | PHP dependency manager.                                                    |
| **Database** | SQLite/MySQL | SQLite is configured by default for zero-config setup.                     |

### Key Dependencies

This kit comes pre-installed with a suite of powerful packages:

- **[Livewire Flux](https://fluxui.dev/)** (`^2.1.1`): A set of modern, accessible UI components.
- **[Livewire Volt](https://livewire.laravel.com/docs/volt)** (`^1.7.0`): Functional API for Livewire components.
- **[Tailwind CSS](https://tailwindcss.com/)** (`^4.0.7`): Utility-first CSS framework, integrated via `@tailwindcss/vite`.
- **[Spatie Permissions](https://spatie.be/docs/laravel-permission)** (`^6.23`): Role and permission management.
- **[Spatie Activitylog](https://spatie.be/docs/laravel-activitylog)** (`^4.10`): Log activity inside your app.
- **[Spatie MediaLibrary](https://spatie.be/docs/laravel-medialibrary)** (`^11.17`): Associate files with models.
- **[Nunomaduro Essentials](https://github.com/nunomaduro/laravel-essentials)** (`^1.0`): A collection of essential PHP and Laravel utilities.

---

## 🚀 Project Overview & Features

**Laravel Super Kit** is designed to kickstart new applications with a solid foundation. It eliminates the repetitive setup of auth, UI scaffolding, and code quality tools.

### Core Features

- **Advanced Authentication**: Powered by Laravel Fortify. Includes Login, Registration, Password Reset, Email Verification, and Two-Factor Authentication (2FA).
- **Modern UI Stack**: Built with Livewire Volt and Flux UI components for a reactive, single-page-app feel without the complexity.
- **Developer Experience**: Pre-configured with `Pest` for testing, `PHPStan` for static analysis, `Rector` for automated refactoring, and `Pint` for code style.
- **Ready-to-Use Settings**: Complete user settings pages (Profile, Password, Appearance, 2FA).
- **Backend Ready**: Database-backed Queue, Sessions, and Cache are configured out-of-the-box (via SQLite) to mirror production behavior locally.

---

## 🛠 Installation

### 1. Create Project

You can create a new project using Composer:

```bash
composer create-project helmordev/laravel-super-kit my-app
cd my-app
```

### 2. Setup (optional)

Run the automated setup script. This handles installing dependencies (PHP & JS), setting up the `.env` file, generating keys, and migrating the database.

```bash
composer setup
```

_What `composer setup` does:_

- Copies `.env.example` to `.env`
- Generates `APP_KEY`
- Creates `database/database.sqlite`
- Runs migrations
- Installs Node dependencies (`pnpm install`)
- Builds assets (`pnpm run build`)

### 3. Start Development

Start the development server, queue worker, and Vite dev server in one command:

```bash
composer dev
```

Or run them individually in separate terminals:

```bash
php artisan serve
php artisan queue:listen --tries=1
pnpm run dev
```

### 4. First Login

The database is seeded with a test user:

- **Email**: `test@example.com`
- **Password**: `password`

---

## ⚙️ Configuration

### Environment Variables

The `.env` file controls your application configuration. Key variables include:

| Variable           | Default            | Description                                                   |
| :----------------- | :----------------- | :------------------------------------------------------------ |
| `APP_NAME`         | `Laravel`          | The name of your application.                                 |
| `APP_URL`          | `http://localhost` | The URL of your app (critical for 2FA/QR codes).              |
| `DB_CONNECTION`    | `sqlite`           | Database driver. Change to `mysql` or `pgsql` for production. |
| `QUEUE_CONNECTION` | `database`         | Driver for background jobs.                                   |
| `SESSION_DRIVER`   | `database`         | Driver for user sessions.                                     |
| `CACHE_STORE`      | `database`         | Driver for caching.                                           |

### Customizing the UI

- **Theme**: Global styles and Tailwind configuration are in `resources/css/app.css`.
- **Layouts**: Modify `resources/views/components/layouts/app.blade.php` to change the application shell.
- **Components**: Reusable UI components are located in `resources/views/components/`.

---

## 📖 Usage Guide

### Creating a New Page (Volt)

1.  **Create the View**: `resources/views/pages/dashboard/users.blade.php`

    ```php
    <?php
    use Livewire\Volt\Component;
    use App\Models\User;

    new class extends Component {
        public function with() {
            return ['users' => User::all()];
        }
    }; ?>

    <x-layouts.app title="Users">
        <flux:heading>User Management</flux:heading>
        <div class="mt-4">
            @foreach($users as $user)
                <div class="p-2 border-b">{{ $user->name }}</div>
            @endforeach
        </div>
    </x-layouts.app>
    ```

2.  **Define the Route**: `routes/web.php`

    ```php
    use Livewire\Volt\Volt;

    Volt::route('/users', 'pages.dashboard.users')
        ->middleware(['auth', 'verified'])
        ->name('users');
    ```

### Using Activity Log

Log important user actions easily:

```php
activity()
   ->causedBy(auth()->user())
   ->event('project-created')
   ->log('Created a new project: ' . $project->name);
```

### Managing Permissions

```php
// Assign role
$user->assignRole('admin');

// Check permission
if ($user->can('edit articles')) {
    // ...
}
```

### Handling Media

Attach files to your models:

```php
$user->addMedia($request->file('avatar'))
     ->toMediaCollection('avatars');
```

---

## 🔧 Troubleshooting

### Common Issues

**1. Tailwind styles are missing**

- Ensure the Vite directive is present in your head: `@vite(['resources/css/app.css', 'resources/js/app.js'])`.
- Run `pnpm run dev` to regenerate styles.

**2. 2FA QR Code not showing or invalid**

- Verify `APP_URL` in `.env` matches exactly the URL you are accessing in the browser.
- Ensure `CACHE_STORE` and `SESSION_DRIVER` are working (default `database` requires migrated tables).

**3. SQLite errors**

- If the database is corrupted or missing, delete `database/database.sqlite` and run:
    ```bash
    touch database/database.sqlite
    php artisan migrate
    ```

---

### Coding Standards

Please ensure your code passes all checks before submitting:

```bash
composer lint   # Fixes style issues
composer test   # Runs tests and static analysis
```

---

## 📄 License

This project is open-sourced software licensed under the [MIT license](https://opensource.org/licenses/MIT).
