# Project Proposal

Pod Members: **Prateek Oblumpally, Brandon Curo, Charles Mada**
Team Name: Big O(3)

## Problem Statement

Themes for brainstorming: Health/Accessiblity

Insert your groups problem statement and target audience.

Health/Accessiblity: 

People with disabilities can't trust online accessibility info.

Target audience: 61M Americans with disabilities

## Description

What is the main purpose of your project? What are the key features your site has to offer its users? How will your targeted audience use your website?

Our project is a community-powered accessibility discovery platform that empowers people with disabilities to find truly accessible venues before they leave home. The main purpose is to solve the critical problem that existing platforms like Google Maps only provide vague "wheelchair accessible" labels without any specific details about ramps, bathroom accessibility, parking, elevators, or sensory accommodations.

Our project provides detailed, photo-verified accessibility profiles with AI-powered computer vision that automatically detects accessibility features (ramps, elevators, wide doors) in uploaded photos, searchable by natural language queries and community-verified accessibility scores.

People with disabilities search for venues with specific accessibility needs, view photo evidence and community reviews to make confident decisions before visiting, while contributors upload photos that get automatically analyzed by AI and verified by the community to build a trustworthy accessibility database.

## Expected Features List

Detailed Venue Accessibility Profiles

Accessibility score (0-100) calculated from verified features
Specific feature breakdown: wheelchair ramps, wide doorways, accessible bathrooms with grab bars, elevator access, accessible parking spots, braille signage, automatic doors
Photo evidence for every claimed feature
Community reviews from actual people with disabilities


AI-Powered Photo Analysis (Primary ML Feature)

Users simply upload photos of venue entrances, bathrooms, parking areas
Computer vision model (PyTorch Grounding DINO, open-vocabulary detection) automatically detects accessibility features in the photo
Returns visual results with bounding boxes showing exactly where the ramp, elevator, or wide door is located
Makes contributing fast and easy — no tedious forms to fill out


Smart Natural Language Search (Secondary AI Feature)

Search using plain English: "coffee shops with quiet spaces and wheelchair accessible bathrooms near me"
Claude AI interprets the query and filters venues by specific accessibility needs
AI-generated explanations: "Blue Bottle Coffee is your best match — it has wheelchair-accessible restrooms verified by 12 users and a dedicated quiet room"

## Related Work

What similar apps and websites? How will your project stand out from these other websites?

1. Google Maps "Accessibility" Feature

Generic "wheelchair accessible" badge with no details (door width, grab bars, etc.).
No photo verification, unverified community contributions.

3. Wheelmap.org
   
Crowdsourced wheelchair accessibility map (Europe-focused).
No photos, only wheelchair users (ignores visual/hearing/sensory needs).

3. Yelp
   
Accessibility info buried in text reviews, no structured filtering.
Not disability-focused.

## Open Questions

What questions do you still have? What topics do you need to research more for your project?

ML Model Accuracy: Can we achieve 85%+ accuracy with only 200-300 training images, or do we need more? What's our backup plan if fine-tuning takes longer than Week 7?

Data Collection: Where will we collect 200+ training images by Week 6? Should we do a team photo outing to local venues, or scrape images from online sources?

User Acquisition: How do we get our first 100 users to contribute data when the platform is empty? Should we pre-seed the database with 20-30 popular venues using manual data entry?

Deployment & Infrastructure: Can Render's free tier handle a Python ML service with acceptable performance (<3 seconds per photo)? Do we need GPU for inference, or is CPU fast enough?

Trust & Safety: How do we prevent abuse (fake photos, spam) with just 3 people on the team? What's our content moderation strategy — community flagging, admin review, or automated filters?
