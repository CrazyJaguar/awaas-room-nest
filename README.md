## RoomNest – Mixed Rental Marketplace MVP

RoomNest is a full-stack Next.js + Firebase MVP that allows renters to discover Rooms, Flats, and Hostels while providing owners and admins with purpose-built dashboards.

### Stack

- Next.js (App Router) + React + Tailwind CSS
- Firebase Auth, Firestore, Storage
- Lucide icons, Framer Motion, clsx

---

## 1. Local Setup

```bash
cd awaas-app
npm install
npm run dev
```

App runs on http://localhost:3000.

---

## 2. Environment variables

Create `.env.local` inside `awaas-app`:

```
NEXT_PUBLIC_FIREBASE_API_KEY=your-key
NEXT_PUBLIC_FIREBASE_AUTH_DOMAIN=your-domain.firebaseapp.com
NEXT_PUBLIC_FIREBASE_PROJECT_ID=your-project
NEXT_PUBLIC_FIREBASE_STORAGE_BUCKET=your-bucket.appspot.com
NEXT_PUBLIC_FIREBASE_MESSAGING_SENDER_ID=000000000
NEXT_PUBLIC_FIREBASE_APP_ID=1:000000000:web:xxxx
NEXT_PUBLIC_FIREBASE_MEASUREMENT_ID=G-XXXXXXX
```

> The first account created with the admin credentials becomes the platform admin automatically.

---

## 3. Firebase configuration

1. Enable **Email/Password** authentication.
2. Create two collections:
   - `users` – documents keyed by `uid`.
   - `listings`.
3. Firestore rules (simplified recommendation):
   - Only admins can toggle `isPublished` and `isApprovedOwner`.
   - Logged-in users can read listings; only owners can write their own.
4. Storage rules: restrict to authenticated users, e.g. allow uploads under `listings/{uid}`.

---

## 4. Core features

- **Homepage `/`**
  - Advanced filter bar (type, location text, price range, dates, guests).
  - Real-time listing grid with skeleton loading + map placeholder.
  - Property cards highlight propertyType, location, pricing.
- **Listing detail `/listing/[id]`**
  - Shows gallery, availability, capacity.
  - `exactAddress` + `ownerPhone` revealed only to authenticated users.
- **Auth**
  - Email/password login & signup.
  - Roles: `user`, `owner`, `admin`; owners require `isApprovedOwner`.
- **Host dashboard `/host/dashboard`**
  - Conditional form fields (Hostel capacity required).
  - Multiple image uploads to Firebase Storage.
  - Draft listings stay unpublished until admin review.
- **Admin dashboard `/admin/dashboard`**
  - View all users and listings.
  - Toggle `isApprovedOwner` and `isPublished`.
- **Saved listings**
  - Heart icon on any card saves/unsaves the property to the user profile.
  - Saved items sync in real time via Firestore and surface on `/profile`.
- **Media pipeline**
  - Owner uploads are auto-resized on the client to 1280×720 for consistent quality.
  - No artificial size limit; images are stored as base64 blobs in Firestore.
- **Auth extras**
  - Email/password plus Google Sign-In powered by Firebase.
  - Admin login is isolated at `/admin/login` so it never conflicts with owner/user roles.
- **Responsive UI**
  - Tailwind-driven layout, skeletons, badges, trust cues.

---

## 5. Data models

### `listings`
```
{
  title: string,
  propertyType: 'Room' | 'Flat' | 'Hostel',
  generalLocation: string,
  exactAddress: string,
  geoPoint: { lat: number, lng: number },
  capacity: number,
  price: number,
  ownerPhone: string,
  ownerId: string,
  ownerEmail: string,
  availability: { start: string, end: string },
  coverImage: string,
  photos: string[],
  isPublished: boolean,
  isFeatured: boolean,
  createdAt: string,
  updatedAt: string
}
```

### `users`
```
{
  uid: string,
  email: string,
  role: 'user' | 'owner' | 'admin',
  isApprovedOwner: boolean,
  createdAt: string
}
```

---

## 6. Admin access

- Navigate to `/admin/login`
- Credentials are currently fixed for the MVP:
  - **Email:** `sushantrm4367@gmail.com`
  - **Password:** `Sushant#12`
- This path bypasses Firebase role checks to avoid conflicts with owner/user flows.

---

## 6. Helpful commands

| Command         | Description                 |
| --------------- | --------------------------- |
| `npm run dev`   | Start local dev server      |
| `npm run build` | Production build            |
| `npm start`     | Run built bundle            |
| `npm run lint`  | ESLint (Next.js config)     |

---

## 7. Testing the flows

1. Sign up as admin with the seeded credentials → admin dashboard becomes available.
2. Sign up as owner → admin approves owner → owner creates listing (images uploaded).
3. Admin toggles `isPublished` → listing appears on home with filters.
4. Log in as renter → open listing detail to reveal protected information.

---

## 8. Next steps

- Add map provider (Mapbox/Google) using stored `geoPoint`.
- Expand booking + messaging flows.
- Harden Firestore/Storage security rules before production.
