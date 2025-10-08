# Menu & Store Integration with MongoDB

## What Was Implemented

The system has been updated so that meals added by owners in the Owner Dashboard now appear in both the Menu page and Stores page, pulling data directly from MongoDB Atlas.

## Changes Made

### 1. Database Schema Updates

**Meal Model** (`/backend/models/Meal.js`)
- Added optional `store` field to link meals to specific stores
- This allows meals to be associated with the store that offers them

### 2. API Enhancements

**API Service** (`/src/services/api.js`)
- Added `getPublicMeals(filters)` - Fetches all available meals for public viewing
- Added `getStores(filters)` - Fetches all stores with filtering options
- Both functions support query parameters for filtering, sorting, and pagination

### 3. Frontend Updates

#### Menu Page (`/src/pages/Menu.jsx`)
- Now fetches real meals from MongoDB via API on page load
- Displays loading spinner while fetching data
- Shows error messages if data fetch fails
- Filters meals by:
  - Search term (name/description)
  - Category (Pancakes, Street Food, South Indian, etc.)
  - Time of day (morning, afternoon, evening)
- Sorts meals by:
  - Highest Rated
  - Price: Low to High
  - Price: High to Low
  - Most Popular (by review count)

#### Stores Page (`/src/pages/Stores.jsx`)
- Now fetches real stores from MongoDB via API on page load
- Displays loading spinner while fetching data
- Shows error messages if data fetch fails
- Filters stores by:
  - Search term (name/specialties)
  - Area (Sakhrale, Takari, Islampur, Walwa)
  - Open status
  - Category filter from URL parameters

## How It Works

### Adding a Meal Flow

1. **Owner logs in** and navigates to Owner Dashboard
2. **Selects time slot** (Morning/Afternoon/Evening)
3. **Clicks "Add New Item"** and fills out the form:
   - Item Name
   - Price
   - Category
   - Preparation Time
   - Description
   - Image URL
   - Tags (optional)
4. **Submits form** → Meal is saved to MongoDB with:
   - `createdBy`: Owner's user ID
   - `timeOfDay`: Selected time slot
   - `isAvailable`: true (by default)
   - All other form data
5. **Meal appears immediately** in Owner Dashboard
6. **After refresh**, meal persists (loaded from database)

### Viewing Meals in Menu Page

1. **User visits Menu page** (`/menu`)
2. **Page loads** → Fetches all available meals from MongoDB
3. **Meals display** in a grid with:
   - Image
   - Name
   - Description
   - Price
   - Rating (if available)
   - Preparation time
   - Tags (Vegetarian, Vegan, etc.)
4. **User can filter** by category, time, or search term
5. **User can sort** by rating, price, or popularity
6. **Click "Show Stores"** → Redirects to Stores page filtered by category

### Viewing Stores

1. **User visits Stores page** (`/stores`)
2. **Page loads** → Fetches all active stores from MongoDB
3. **Stores display** with:
   - Name
   - Rating
   - Address
   - Phone
   - Specialties
   - Features
   - Status (Open/Closed)
4. **User can filter** by area, search term, or open status
5. **Click "View Menu"** → Redirects to Menu page with store filter

## Data Flow Diagram

```
Owner Dashboard
      ↓
   POST /api/meals
      ↓
   MongoDB Atlas
      ↓
   GET /api/meals (public)
      ↓
   Menu Page
```

```
Store Registration
      ↓
   POST /api/stores
      ↓
   MongoDB Atlas
      ↓
   GET /api/stores
      ↓
   Stores Page
```

## Key Features

### Real-Time Updates
- Meals added by owners are immediately available in the public menu
- No manual refresh needed after adding a meal
- Success/error messages provide clear feedback

### Filtering & Sorting
- **Menu Page**:
  - Filter by category (8 categories)
  - Filter by time of day (morning/afternoon/evening)
  - Search by name or description
  - Sort by rating, price, or popularity

- **Stores Page**:
  - Filter by area (4 areas)
  - Filter by open status
  - Search by name or specialties
  - Filter by category from URL

### Responsive Design
- Works seamlessly on mobile, tablet, and desktop
- Loading states prevent confusion
- Error states inform users of issues
- Empty states guide users when no results found

## Testing the Integration

### Test 1: Add a New Meal
1. Log in as owner (use credentials from OWNER_CREDENTIALS.md)
2. Go to Owner Dashboard
3. Select a time slot
4. Add a new meal with all required fields
5. Verify meal appears in dashboard
6. Go to Menu page
7. Verify new meal appears in the list
8. Filter by the meal's category
9. Verify meal still appears

### Test 2: Store-Meal Association
1. Add stores to MongoDB (if not already present)
2. Associate meals with stores (via store field)
3. Visit Stores page
4. Click "View Menu" on a store
5. Verify only that store's meals appear (when implemented)

### Test 3: Filtering & Sorting
1. Visit Menu page
2. Try different category filters
3. Try different time filters
4. Search for specific meals
5. Try different sort options
6. Verify results update correctly

## Future Enhancements

### Short-term
- Link meals to specific stores in Owner Dashboard
- Filter Menu page by store
- Show store availability for each meal
- Add meal images upload functionality

### Long-term
- Real-time availability updates
- Order placement from Menu page
- User reviews and ratings
- Meal recommendations
- Inventory management
- Analytics dashboard for owners

## Technical Notes

### API Endpoints Used
- `GET /api/meals` - Fetch meals (with optional filters)
- `POST /api/meals` - Create new meal (authenticated)
- `PUT /api/meals/:id` - Update meal (authenticated)
- `DELETE /api/meals/:id` - Delete meal (authenticated)
- `GET /api/stores` - Fetch stores (with optional filters)

### Authentication
- Uses JWT tokens stored in localStorage
- Token sent in Authorization header as `Bearer <token>`
- Owner role required for creating/updating/deleting meals
- Public endpoints don't require authentication

### Error Handling
- Network errors displayed with clear messages
- Validation errors show specific field issues
- Loading states prevent user confusion
- Empty states guide users to next action
