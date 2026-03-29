# Challenge Lab: Mode 2

In this challenge, you'll use a visual platform to build a complete web application , with database, authentication, and automatic deployment.

## The Challenge

Build a **service directory** (a simple marketplace) with:

1. A listing page with filters (category, location)
2. Profile pages for each service
3. Registration/login for providers
4. A form to add new services
5. A database that persists the information

**Estimated time**: 4-6 hours

**Allowed tools**: A visual platform (Lovable, Bolt, or Base44) + Supabase. No local IDE.

## Suggested steps

### Step 1: Choose the platform (15 min)

- **Lovable** if you want the most complete app with minimum effort
- **Bolt.new** if you want more control over the code
- **Base44** if your app is mostly data and CRUD

### Step 2: Describe the app (30 min)

In your chosen platform, describe:

> "Create a local services directory. The main page shows a list of services in cards with name, category, short description, and location. There are category filters and a search bar. Each card links to a detail page with full information and contact. Providers can register and add their services."

### Step 3: Set up the database (1h)

Connect Supabase and define the tables:

- `services`: id, name, category, description, location, contact, provider_id
- `providers`: id, email, name, created_at

Ask the platform to connect the frontend to the tables.

### Step 4: Add authentication (1h)

Configure registration/login with Supabase Auth. Providers should be able to:

- Register with email
- Log in
- View and edit their own services

### Step 5: Iterate the design (1h)

- "Improve the cards with placeholder images"
- "Add pagination to the listing"
- "Improve the service addition form with validation"

### Step 6: Test and deploy (30 min)

1. Test all flows: register, login, add service, search, filter
2. Deploy with the platform
3. Test the public URL

## Success criteria

- [ ] The listing shows services from the database
- [ ] Filters work
- [ ] Registration and login work
- [ ] A provider can add a new service
- [ ] The app is online with a public URL
- [ ] Data persists between sessions

## Bonus

- Add reviews/ratings for services
- Add image upload
- Add email notification when a new service is added
- Implement full-text search
