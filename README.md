# DentalFlow

DentalFlow is a multi-tenant dental practice management SaaS built with
Flutter, Riverpod, Material 3, Supabase Auth, PostgreSQL and Row Level
Security.

This first increment contains:

- A clean, feature-first Flutter structure.
- Responsive email/password and mobile OTP authentication.
- Password-reset support.
- Auth-aware routing and a protected dashboard placeholder.
- A production-oriented PostgreSQL schema.
- Tenant isolation using `clinic_id`, composite foreign keys and RLS.
- Role-aware policies for super admins, owners, dentists, receptionists and
  assistants.
- Patient medical-history fields derived from the supplied case-history form.
- Prescription items and medicine templates derived from the supplied sample.
- Private patient-file storage policies.
- Local dummy clinic, patient, history and medicine-template seed data.

## 1. Prerequisites

- Flutter stable with Dart available on `PATH`
- Supabase CLI
- Docker Desktop for the local Supabase stack

Flutter was not installed on the machine when this increment was generated.
After installation, run:

```powershell
.\scripts\bootstrap_flutter.ps1
```

That creates Android, iOS and Web platform folders, restores packages, runs
analysis and executes tests without replacing the supplied `lib` sources.

## 2. Start Supabase

```powershell
supabase start
supabase db reset
```

For a hosted project, link it and push the migration:

```powershell
supabase link --project-ref YOUR_PROJECT_REF
supabase db push
```

## 3. Create clinic staff safely

Public self-signup is disabled. Create users through a trusted server-side
admin workflow and place tenant assignment in `app_metadata`, not
`user_metadata`.

Example metadata for a clinic owner:

```json
{
  "clinic_id": "10000000-0000-0000-0000-000000000001",
  "role": "clinic_owner"
}
```

Supported roles:

- `super_admin` (must not have a clinic ID)
- `clinic_owner`
- `dentist`
- `receptionist`
- `assistant`

The database trigger creates the matching `user_profiles` row. A clinic role
without trusted `clinic_id` metadata is rejected.

## 4. Run the application

Use the local or hosted Supabase values:

```powershell
flutter run `
  --dart-define=SUPABASE_URL=http://127.0.0.1:54321 `
  --dart-define=SUPABASE_PUBLISHABLE_KEY=YOUR_PUBLISHABLE_KEY
```

For Web:

```powershell
flutter run -d chrome `
  --dart-define=SUPABASE_URL=http://127.0.0.1:54321 `
  --dart-define=SUPABASE_PUBLISHABLE_KEY=YOUR_PUBLISHABLE_KEY
```

Never place a Supabase service-role key in the Flutter application.

## 5. Mobile OTP

Configure an SMS provider under Supabase Authentication settings. Phone
numbers must use international E.164 format, such as `+919876543210`.

## 6. Storage convention

Upload patient files under:

```text
<clinic_uuid>/<patient_uuid>/<generated-file-name>
```

Storage RLS reads the first path segment and verifies clinic access.

## Structure

```text
lib/
  core/
    config/
    providers/
    routing/
    theme/
  features/
    auth/
      data/
      domain/
      presentation/
      providers/
    dashboard/
supabase/
  migrations/
  config.toml
  seed.sql
```

## Next increment

The next logical feature is the responsive application shell and patient
management module: registration, case history, search, profile tabs and
timeline.

