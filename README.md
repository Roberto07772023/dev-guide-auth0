# Customizing Auth0 Login Forms: A Developer's Guide

This guide is based on hands-on experimentation with Auth0's development environment, their documentation, and extensive trial-and-error. We break down the main customization options, step by step, with pros and cons, and highlight potential roadblocks. The goal is to help you choose the best approach for your project without wasting time. This guide was created by Ashedzi Solomon and Sarah Mitchell, with assistance from Neralake, Floodline Consulting, and Manitoba Innovates.

## Why Customize Auth0 Login Forms?

Auth0's default Universal Login forms are secure and handle sign-up, login, and password resets out of the box. However, they lack branding, and if you want to capture additional data (e.g., a user's preferred name during sign-up) or create a seamless app experience, customization is essential.

**Note**: Auth0 recommends Universal Login for most cases due to its hosted nature, ensuring better security (automatic vulnerability updates) and easier maintenance. For deeper customizations, you may need to explore beyond the defaults.

![Original Auth(0) Login](/img/ogAuth0.png)

## Option 1: Using the Auth0 Dashboard (No-Code Editor for Universal Login)

The quickest way to customize if you're not comfortable coding or need only basic changes.

### How to Do It

1. Log into your Auth0 account and navigate to **Branding > Universal Login**.
2. Ensure the "New" experience is selected (default for most tenants).
3. Upload your logo, choose colors (primary for buttons, background for the page), select a font, and adjust text strings for buttons and prompts.
4. Preview changes in the dashboard—Auth0 provides a live mockup.
5. Save and test by triggering a login from your app.

### Pros and Cons

- **Pros**: Simple, no coding required, secure (Auth0 hosts and updates automatically). Changes apply instantly across all apps in the tenant. Ideal for teams without dedicated auth developers.
- **Cons**: Limited to basic tweaks. No custom CSS for advanced styling (e.g., transparency or layout changes). Lacks version control, requiring manual reversion of changes.

### Examples Made Using Dashboard

![Dashboard Example 1](/img/exampleDashboard1.png)
![Dashboard Example 2](/img/exampleDashboard2.png)
![Dashboard Example 3](/img/exampleDashboard3.png)
![Dashboard Example 4](/img/exampleDashboard4.png)

## Option 2: Using Liquid Language (Page Templates for Universal Login)

For deeper customization within Universal Login, Liquid templates allow HTML, CSS, and dynamic logic. Requires a custom domain to unlock "advanced mode."

### How to Do It

1. Set up a custom domain in Auth0 (**Settings > Custom Domains**). Add a CNAME record in your DNS to verify the domain and enable advanced features.
2. Go to **Branding > Universal Login > Advanced Options > Page Templates**.
3. Download the default Liquid template as a starting point.
4. Edit the HTML in your preferred editor (e.g., VS Code). Use Liquid tags for dynamics:
   - `{{ application.name }}` for your app's name.
   - `{% if prompt.name == 'login' %}` for conditional logic (e.g., different text for login vs. sign-up).
5. Use Postman to send PUT requests with the edited Liquid templates via the Auth0 Management API.
6. Test by logging in—changes apply immediately.

### Pros and Cons

- **Pros**: Full HTML/CSS control within Auth0's secure hosting. Supports dynamic elements (e.g., user-specific greetings or conditional fields). Fast and secure with Auth0 handling updates.
- **Cons**: Requires a custom domain, adding DNS setup and potential downtime if misconfigured. Liquid syntax has a learning curve. Updates via API are less instant than dashboard tweaks. Limited JavaScript support, as Liquid is for templating, not complex scripts.

### Example Made with Liquid Language

![Liquid Example 1](/img/exampleLiquid1.png)
![Liquid Example 2](/img/exampleLiquid2.png)

## Option 3: Switching to Classic Login and Editing CSS/JS

For maximum flexibility, switch to the deprecated Classic Login to use Auth0's Lock widget or build a fully custom UI embedded in your app. **Not recommended**, as it is no longer updated or supported by Auth0.

### How to Do It

1. In the dashboard, go to **Branding > Universal Login > Advanced Options > Login** and select "Use Classic Experience."
2. Embed the Lock widget in your app's code (e.g., in clienthub's login route):
   - Include the Lock script: `<script src="https://cdn.auth0.com/js/lock/13.0/lock.min.js"></script>`
   - Initialize Lock with custom options: `var lock = new Auth0Lock('YOUR_CLIENT_ID', 'YOUR_DOMAIN', { theme: { primaryColor: '#ffffff', background: 'transparent' } });`
3. Test locally and deploy.

### Pros and Cons

- **Pros**: Complete control over CSS/JS, custom fields (e.g., preferred name), and integration with your app's code (e.g., React components). No custom domain required. Suitable for legacy apps or deep integration needs.
- **Cons**: Deprecated, missing new features and security updates. More vulnerable since you host or embed the code. Higher maintenance, as you handle updates. Less seamless for multi-app tenants.

### Example Made with Classic Login

![Classic Example 1](/img/exampleClassic1.png)

## Option 4: Using the Management API and Postman for Customization

For programmatic control without dashboard reliance, use the Auth0 Management API with Postman, a free API client for building, testing, and sharing API requests.

### What is Postman?

Postman is a popular API client with a GUI for headers, bodies, and responses, ideal for experimenting with APIs like Auth0's. Download it from [postman.com](https://postman.com) (free for basics, with a web version available).

### How to Customize with Management API and Postman

Auth0's Management API allows programmatic management of tenant settings, such as branding or prompt text. Here's how to use it with Postman:

1. **Get a Token**: In the dashboard, go to **Applications > APIs > Auth0 Management API > API Explorer** and select scopes like `update:branding` or `read:branding`.
2. **Install Postman**: Download from [postman.com](https://postman.com) and sign in (optional).
3. **Check Available Endpoints**:
   - Review Auth0 Management API docs under Branding for endpoints:
     - `GET /api/v2/branding/themes` (get current themes)
     - `POST /api/v2/branding/themes` (create a new theme)
     - `PATCH /api/v2/branding/themes/{id}` (update an existing theme)
4. **Get the Current Theme**:
   - In Postman, create a new request: `GET https://your-tenant-name.us.auth0.com/api/v2/branding/themes`.
   - Set **Authorization** to `Bearer Token` and paste your Management API token.
   - Send the request. The response returns a a JSON object with current theme settings (colors, fonts, etc.) and a `themeId`.
   - Copy this JSON as your template/guide.
5. **Create a New Theme**:
   - Create a new request: `POST https://your-tenant-name.us.auth0.com/api/v2/branding/themes`.
   - In Postman, go to **Body > raw > JSON**, paste the copied JSON (remove the `themeId`), and edit values (e.g., colors, fonts).
   - Send to create a new theme.
6. **Update the Default Theme**:
   - Copy the new `themeId` from the POST response.
   - Create a new request: `PATCH https://your-tenant-name.us.auth0.com/api/v2/branding/themes/{themeId}`.
   - In the body, paste the updated JSON (include the `themeId` in the URL).
   - Send to apply the updated theme to Universal Login.
7. **Text Customization**:
   - Create a new request: `PUT https://your-tenant-name.us.auth0.com/api/v2/prompts/login/custom-text/en`.
   - Body: `{"login": {"title": "Welcome Back"}}`.
8. **Test and Iterate**: Use Postman to view responses and verify changes in Auth0's preview.

**Note**: Changes apply tenant-wide, so test in a dev environment first.

### Pros and Cons

- **Pros**: Automates changes, ideal for DevOps pipelines. Full programmatic control over branding. Postman simplifies testing and iteration.
- **Cons**: Requires learning API endpoints and scopes. Rate limits can slow things down. No version control for changes.

### Examples Made with Management Api + Postman

![Management API Example](/img/exampleMGMT1.png)


## Comparison Table

| Method            | Pros                                                                 | Cons                                                                 | Best For                              |
|-------------------|----------------------------------------------------------------------|----------------------------------------------------------------------|---------------------------------------|
| Dashboard (No-Code) | Quick, no code, secure, instant updates                              | Limited to basics, no version control                                 | Simple branding, non-technical teams  |
| Liquid Templates  | Full HTML/CSS, dynamic logic, secure hosting                          | Needs custom domain, Liquid learning curve                            | Advanced branding without full custom UI |
| Classic Login     | Maximum flexibility, custom code, no domain needed                    | Deprecated, less secure, high maintenance                            | Legacy systems, extreme customization |
| Management API    | Automates changes, programmatic control, Postman simplifies testing   | Requires API knowledge, rate limits, no version control               | DevOps automation                     |

## Examples from Real Sites

- **Open AI**: ![Open AI login](/img/exampleOther3.png)
- **Nutmeg**: ![Nutmeg login](/img/exampleOther4.png)
- **Wyndham Rewards**: ![Wyndham login](/img/exampleOther5.png)

## Conclusion

Customizing Auth0 forms enhances your app's branding and user experience. Start with the dashboard for simplicity and scale up to liquid templates based on your needs. Test thoroughly in a dev environment to avoid disruptions. Happy customizing!

## Credits

Created by Ashedzi Solomon and Sarah Mitchell, with assistance from Neralake, Floodline Consulting, and Manitoba Innovates.
