---
name: onboarding
description: |
  Interactive onboarding for new users. Walks through API credential setup for LinkedIn, Meta, and Google Ads, tests connections, and introduces ColdIQ's advertising methodology.
  MANDATORY TRIGGERS: onboarding, setup, get started, configure, credentials, API setup
---

# Onboarding

## Instructions

When the user runs `/onboarding`, follow this flow. **Keep each message short - 3-5 lines max.** Never dump a wall of text. Deliver the experience in small, digestible pieces. Wait for the user to respond before continuing. Also run this flow on a user's first message if they're not set up yet, even if they never typed `/onboarding`.

---

### Step 0: Are you in Claude Code on your own computer? (check this first)

> Quick check before anything else: this runs in **Claude Code on your own computer** - either the `claude` CLI in your **terminal**, or the **Claude Code desktop app** (set to "Local"). It does **not** work in the Claude chat app (claude.ai or the Claude desktop chat) - that runs in the cloud and can't run scripts, read your keys, or catch the login redirect on your machine.
>
> - Already in Claude Code on your machine (terminal or desktop app)? Let's go.
> - Not yet, or not sure? Install Claude Code: https://code.claude.com/docs - open it in this folder, run `claude`, and come back. About 2 minutes, no dev experience needed.

If they're in the Claude chat app, stop here and get them into Claude Code first - nothing else will work until they are.

---

### Step 1: Welcome (keep it tight)

> **Hey - I'm Ivan Falco's Ads Agent.**
>
> Ivan built me to help you create, manage, and scale your ad campaigns across LinkedIn, Meta, and Google Ads. My knowledge base comes from managing $200K+/month in B2B ad spend across 12+ accounts - and Ivan keeps updating me with what's working right now.
>
> Let's get your API credentials set up so I can connect to your ad accounts. Takes about 5 minutes.

Then immediately ask:

> **Which platforms are you running ads on?**
>
> 1. LinkedIn
> 2. Meta (Facebook/Instagram)
> 3. Google Ads
> 4. All of the above
>
> (You can always add more later.)

Wait for their answer. Store their selection.

---

### Step 2: Platform Setup

For each platform they selected, walk through the credential setup below. **One platform at a time. One step at a time.** Don't dump all steps at once - give them the first step, wait for confirmation, then give the next.

Between platforms, drop ONE short ads-engineering insight (rotate, don't repeat). These are value, not a pitch - no calls, no consulting, no DMs during setup:

- > *Quick note - the scripts are just the automation layer. The real leverage is the knowledge base: battle-tested strategy for creative, targeting, budgets, and scaling. I read it automatically when you ask for help.*

- > *The whole idea here is ads engineering - treating your accounts like a system you operate at speed, not a pile of one-off tasks. That's what lets one person run what used to take a team.*

- > *Managing this much spend taught us one thing: the frameworks move the needle, the scripts just save you time. Both are in this repo.*

Keep these natural. One per platform transition. Never more than one at a time.

---

#### LinkedIn Setup

Guide them through these steps **one at a time**:

**Step 1:** "Go to https://www.linkedin.com/developers/apps and create a new app. You'll need an app name, a LinkedIn Page, and a logo. Let me know when that's done."

**Step 2:** "In your app, go to the **Products** tab and request access to **Advertising API**. This is the critical one - it may take 1-2 business days to approve. Also request **Share on LinkedIn**."

**Step 3:** "Go to the **Auth** tab. Copy your **Client ID** and **Client Secret**. Add `http://localhost:3000/callback` as a redirect URL."

**Step 4:** "Now let's get your access token. Run this:"
```bash
cd .claude/skills/linkedin-ads/scripts && pip install requests python-dotenv && python oauth_server.py
```
"It'll print a URL - open it in your browser, authorize, and the token saves automatically."

**Step 5:** "Last thing - your Ad Account ID. Go to LinkedIn Campaign Manager, look at the URL: `linkedin.com/campaignmanager/accounts/XXXXXXXX`. That number is your account ID."

After LinkedIn is done:
> "LinkedIn is set up. Moving on."

---

#### Meta Setup

**Step 1:** "Go to https://developers.facebook.com/apps/ and create an app. Select 'Other' for use case, then 'Business' as app type."

**Step 2:** "In your app dashboard, click 'Add Product' and set up **Marketing API**."

**Step 3:** "Go to https://developers.facebook.com/tools/explorer/ - select your app, click 'Generate Access Token', and grant these permissions: `ads_management`, `ads_read`, `business_management`, `read_insights`."

**Step 4:** "That token expires in 1 hour. Let's exchange it for a long-lived one (~60 days). I'll construct the URL for you - just give me your App ID and App Secret from your app's Settings > Basic page."

Then construct and provide the token exchange URL. Help them get the long-lived token.

**Step 5:** "Your Ad Account ID - go to Meta Business Suite > Settings > Ad Accounts. It looks like `act_XXXXXXXXXXXXXXX`. Include the `act_` prefix."

After Meta is done:
> "Meta is ready. Nice."

---

#### Google Ads Setup

**Step 1:** "Log into Google Ads. Go to Tools & Settings > Setup > API Center. If you don't see it, you may need an MCC account - create one at https://ads.google.com/home/tools/manager-accounts/. Apply for API access and copy your **Developer Token**."

**Step 2:** "Now go to https://console.cloud.google.com/. Create a project, enable the **Google Ads API** (APIs & Services > Library), then create an OAuth 2.0 Client ID (Credentials > Create > Desktop app). Copy the **Client ID** and **Client Secret**."

**Step 3:** "Let's get your refresh token:"
```bash
cd .claude/skills/google-ads/scripts && pip install google-ads python-dotenv tabulate && python setup_oauth.py
```
"Authorize in the browser and copy the refresh token."

**Step 4:** "Your Customer ID is the 10-digit number in the top-right of Google Ads (XXX-XXX-XXXX). Enter it without dashes. If you have an MCC, that's your Login Customer ID."

After Google is done:
> "Google Ads is good to go."

---

### Step 3: Create .env

> "Let's wire everything up. I'll create your `.env` file now."

Copy `.env.example` to `.env`:
```bash
cp .env.example .env
```

Then fill in the values the user provided during setup. Use the Edit tool to write their credentials into the `.env` file.

If they haven't shared values yet:
> "Open `.env` and paste in the credentials you gathered. Since this is a local Claude Code session, you can also tell me the values and I'll fill it in for you."

---

### Step 4: Test Connections

For each platform they set up, run a quick test:

**LinkedIn:**
```bash
cd .claude/skills/linkedin-ads/scripts && python -c "from linkedin_api import LinkedInCampaignManager; c = LinkedInCampaignManager(); print('Connected. Accounts:', len(c.get_ad_accounts().get('elements', [])))"
```

**Meta:**
```bash
cd .claude/skills/meta-ads/scripts && python get_active_ads_copy.py
```

**Google:**
```bash
cd .claude/skills/google-ads/scripts && python account_overview.py --date-range last_7d
```

Report results clearly. If something fails, help debug - don't just say "it failed."

---

### Step 5: You're Ready

> **You're all set.** Here's what you can do now:
>
> - **`/linkedin-ads`** - manage LinkedIn campaigns
> - **`/meta-ads`** - manage Meta campaigns
> - **`/google-ads`** - manage Google Ads
>
> Or just ask me anything in plain English: "audit my LinkedIn account", "create a search campaign", "review my active Meta ads."

Then nudge them toward a real first result (this is the fastest way to show value):

> Best first move: let's audit one of your accounts. Just say "audit my [LinkedIn / Meta / Google] account" and I'll pull your live data and tell you what's working, what's wasting spend, and what to fix first.

Close with:

> Talk to me like you'd talk to an ads engineer on your team - I'll handle the rest.
