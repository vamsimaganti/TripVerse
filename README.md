# WanderIndia Tours - Tourism Package Booking Website

A full-stack tourism website: browse packages, register/login, book a package,
enter tourist details, pay (simulated), and view booking confirmation.

- **Frontend:** HTML5, CSS3, Bootstrap 5, vanilla JavaScript (fetch API)
- **Backend:** Java 17, Spring Boot 3.2.5 (Spring Web, Spring Data JPA, Bean Validation)
- **Database:** H2 in-memory database
- **Auth:** Custom session-based auth (HttpSession) with BCrypt password hashing

---

## 1. Project structure

```
tourism-website/
├── pom.xml
├── src/main/java/com/tourism/
│   ├── TourismApplication.java
│   ├── entity/           (User, TlPackages, Booking)
│   ├── repository/       (Spring Data JPA repositories)
│   ├── service/           (UserService, PackageService, BookingService)
│   ├── controller/        (AuthController, PackageController, BookingController)
│   ├── dto/                (request/response objects)
│   ├── config/             (DataInitializer, AppConfig, GlobalExceptionHandler)
│   └── util/                (ApiException, SessionUtil)
└── src/main/resources/
    ├── application.properties
    └── static/               (index.html, login.html, register.html, booking.html,
                                 confirmation.html, my-bookings.html, css/, js/)
```

## 2. How to import into Spring Tool Suite (STS)

1. Unzip the project anywhere on disk.
2. Open STS → `File` → `Import` → `Maven` → `Existing Maven Projects`.
3. Browse to the unzipped `tourism-website` folder → `Finish`.
4. Wait for Maven to download dependencies (needs internet access the first time).
5. Right-click the project → `Run As` → `Spring Boot App`
   (or run the `TourismApplication` class directly).
6. Once it starts, open a browser at:
   - **Website:** http://localhost:8080/index.html
   - **H2 Console:** http://localhost:8080/h2-console
     - JDBC URL: `jdbc:h2:mem:tourismdb`
     - Username: `sa`, Password: *(leave blank)*

## 3. How to run from command line (alternative to STS)

```bash
cd tourism-website
mvn spring-boot:run
```
or build a jar and run it:
```bash
mvn clean package
java -jar target/tourism-website.jar
```

## 4. Demo login

A demo account is auto-created on startup:
- **Email:** demo@tourism.com
- **Password:** demo1234

You can also register a brand-new account from the "Create Account" page.

## 5. User flow

1. **Landing page** (`index.html`) — shows all packages from `tl_packages` with
   background images, price, duration, rating. Login / Create Account buttons
   appear top-right if not logged in.
2. Anyone can **browse** packages without logging in.
3. Clicking **Book Now** checks login status:
   - Not logged in → redirected to `login.html`, then automatically continues
     to booking after successful login.
   - Logged in → goes straight to `booking.html?packageId=X`.
4. **Booking page** — Step 1: enter tourist details (name, email, phone,
   number of travelers, travel date). Step 2: enter payment (card) details.
5. On successful (simulated) payment, redirected to `confirmation.html` showing
   the full booking + payment summary.
6. **My Bookings** page lists all of a user's past bookings.

## 6. Database tables created

- `tl_users` — registered users (passwords stored as BCrypt hashes)
- `tl_packages` — tourism packages (auto-seeded with 9 sample packages on first run)
- `tl_bookings` — bookings, linked to `tl_users` and `tl_packages`, with tourist
  details, payment status, transaction id, and booking status

Tables are auto-created by Hibernate (`spring.jpa.hibernate.ddl-auto=update`)
and packages/demo-user are seeded by `DataInitializer` on startup. Since H2 is
in-memory, all data resets every time the app restarts.

## 7. REST API summary

| Method | Endpoint                 | Description                              | Auth required |
|--------|---------------------------|-------------------------------------------|----------------|
| GET    | /api/packages              | List all packages                          | No             |
| GET    | /api/packages/{id}          | Package details                            | No             |
| POST   | /api/auth/register           | Create account                             | No             |
| POST   | /api/auth/login               | Login (creates session)                    | No             |
| POST   | /api/auth/logout               | Logout (invalidates session)               | Yes            |
| GET    | /api/auth/me                    | Get current logged-in user                 | Yes            |
| POST   | /api/bookings                     | Create a booking (before payment)          | Yes            |
| POST   | /api/bookings/{id}/pay              | Simulate payment for a booking             | Yes            |
| GET    | /api/bookings/{id}                    | Get one booking (must be owner)            | Yes            |
| GET    | /api/bookings/my                        | List logged-in user's bookings             | Yes            |

## 8. Notes / things you can extend later

- Payment is **simulated** — any card number of 12-19 digits and CVV of 3-4
  digits will succeed. Swap `BookingService.processPayment()` for a real
  gateway (Razorpay/Stripe) integration later.
- Swap H2 for MySQL/PostgreSQL by changing `application.properties` and adding
  the relevant JDBC driver dependency — entity code doesn't need to change.
- Package images use `picsum.photos` seeded placeholder images; replace
  `imageUrl` values in `DataInitializer` with real image URLs/uploads as needed.
- Add an admin UI to manage `tl_packages` (currently seeded via `DataInitializer`).
