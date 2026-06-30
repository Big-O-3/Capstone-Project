# User Stories

Pod Members: **Prateek Oblumpally, Brandon Curo, Charles Mada**

## Problem Statement

People with disabilities can't trust online accessibility information. Existing platforms only provide vague "wheelchair accessible" labels without specific details. Our target audience is 61 million Americans with disabilities who need detailed, verified accessibility information before visiting venues.

## User Roles

1. **Venue Visitor**: Person with disabilities who searches for and views accessibility information to decide where to visit.

2. **Contributor**: Community member who uploads photos and reviews to build the accessibility database.

## User Personas

### Venue Visitor Personas

**Maria Rodriguez**
34-year-old graphic designer in Austin who uses a wheelchair. Highly tech-savvy and relies on her smartphone daily. She's been disappointed by inaccurate "wheelchair accessible" labels on Google Maps and needs detailed, photo-verified information about door widths, bathroom grab bars, and table heights before visiting new locations.

**James Chen**
68-year-old retired teacher in Seattle who is blind and uses a guide dog. Moderately comfortable with screen readers on iPhone. He relies on word-of-mouth for restaurant recommendations and wants a centralized platform to search for venues with braille menus, quiet environments, and step-free entrances.

### Contributor Personas

**Sarah Williams**
27-year-old occupational therapy student in Chicago passionate about disability advocacy. Extremely active on social media and loves crowdsourced platforms. She frequently photographs accessibility features and wants to make a meaningful impact helping people with disabilities find accessible spaces.

**David Okafor**
42-year-old software engineer in Boston who uses crutches. Highly technical and wants to improve accessibility data but only if the contribution process is quick (5-10 minutes max) and doesn't require tedious forms.

## User Stories

1. **As a venue visitor, I want to search for venues using natural language, so that I can quickly find places meeting my specific needs without complex filters.**

2. **As a venue visitor, I want to view photo evidence of accessibility features, so that I can verify information before visiting.**

3. **As a venue visitor, I want to see an accessibility score (0-100) for each venue, so that I can quickly compare options.**

4. **As a venue visitor, I want to read reviews from other people with disabilities, so that I can learn about real experiences.**

5. **As a contributor, I want to upload photos without filling out forms, so that I can quickly document venues.**

6. **As a contributor, I want AI to automatically detect accessibility features in my photos, so that I don't have to manually tag them.**

7. **As a contributor, I want to see how many people my contributions helped, so that I feel motivated to continue.**

8. **As a venue visitor, I want to filter results by distance and features, so that I can find the nearest venue meeting all my needs.**

9. **As a contributor, I want photos verified by the community, so that I can trust the platform maintains accurate information.**

10. **As a venue visitor, I want to save favorite venues, so that I can quickly access places I trust.**

## AI Feature User Story

**As a venue visitor, I want to see AI-verified accessibility features with visual evidence, so that I can trust the information is accurate and objective before I visit.**

This connects to our computer vision feature (PyTorch YOLOv5). When visitors view a venue, they see photos with bounding boxes showing exactly where accessibility features are located (ramps, grab bars, elevators). The AI detection ensures consistent, objective verification rather than relying solely on subjective descriptions. This means visitors get fast, reliable information because contributors can upload photos easily, and the AI automatically identifies features - leading to more comprehensive data and trustworthy accessibility information.

## Decisions Log

**Stories refined for scope:**
- Cut "As a venue visitor, I want to find accessible venues" (too broad) → broke into stories #1-4
- Cut "As a contributor, I want the AI model to analyze photos using computer vision" (too technical) → revised to story #6 focusing on user benefit

**Stories cut:**
- "Want app to use location automatically" (implementation detail, not user value)
- "Want photos stored in database" (pure implementation)

**AI feature evolution:**
- Original focused on speed ("analyze quickly") → revised to focus on eliminating manual work 
