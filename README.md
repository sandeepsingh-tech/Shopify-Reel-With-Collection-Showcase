# Reel & Collection Showcase for Shopify (Online Store 2.0)

A high-converting, responsive Shopify section that pairs a 9:16 portrait video (Instagram Reel / TikTok style) directly with a dynamic product collection feed.

---

## Key Features

* **Instagram/TikTok Reel Card:** Dedicated 9:16 portrait video player supporting native Shopify-hosted MP4 files, external URLs (YouTube/Vimeo), or fallback poster images with an animated live status badge.
* **Synchronized Desktop Grid:** Automatically aligns the reel video flush against a balanced 3×2 product grid (6 products) without awkward gaps or bottom overhang.
* **Mobile-First Touch Experience:** Reorganizes seamlessly on mobile devices into a full-width reel preview followed by a compact 2×2 touch-friendly product card grid.
* **Dynamic Collection Integration:** Connects directly to any Shopify collection via the native theme editor—swap collections instantly without touching theme code.
* **Direct Route Integration:** Uses Shopify’s native `routes.cart_add_url` to support direct AJAX cart additions and localized checkout loops safely.
* **Zero Theme Dependency:** Completely self-contained Liquid, CSS, and schema architecture. Can be added to Dawn or any Online Store 2.0 theme without conflicting with global stylesheets.

---

## Installation & Setup Guide

### Step 1: Create the Liquid Section

1. From your **Shopify Admin**, go to **Online Store** > **Themes**.
2. Click the three dots (`...`) next to your published or draft theme, then click **Edit code**.
3. In the left navigation menu, locate the **Sections** folder and click **Add a new section**.
4. Set the section name to `reel-collection-showcase` (Shopify will automatically add the `.liquid` extension).
5. Delete any placeholder code inside the new file.
6. Copy the complete code snippet below, paste it into the file, and click **Save**.

```liquid
{% comment %}
  Section: Reel & Collection
  - Desktop: 9:16 Video player aligned with a 6-product (3x2) grid
  - Mobile: Full-width Reel preview followed by a 4-product (2x2) grid
{% endcomment %}

{%- liquid
  assign collection = section.settings.selected_collection
  assign section_heading = section.settings.heading
  if section_heading == blank and collection != blank
    assign section_heading = collection.title
  endif
-%}

<div class="hm-reel-showcase color-{{ section.settings.color_scheme }} section-{{ section.id }}-padding">
  <div class="hm-reel-container">
    
    <!-- Header Section -->
    <div class="hm-reel-header">
      <div class="hm-reel-title-wrap">
        {%- if section.settings.subheading != blank -%}
          <div class="hm-reel-subheading-pill">
            <span class="hm-pulse-dot"></span>
            <span class="hm-reel-subheading">{{ section.settings.subheading | escape }}</span>
          </div>
        {%- endif -%}
        <h2 class="hm-reel-heading">
          {{ section_heading | default: 'Featured Collection' }}
          <span class="hm-heading-underline"></span>
        </h2>
      </div>

      {%- if collection != blank -%}
        <a href="{{ collection.url }}" class="hm-reel-view-all">
          <span>View All</span>
          <svg class="hm-arrow-icon" width="16" height="16" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg">
            <path d="M3.33334 8H12.6667M12.6667 8L8.00001 3.33334M12.6667 8L8.00001 12.6667" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round"/>
          </svg>
        </a>
      {%- endif -%}
    </div>

    <!-- Main Body Grid -->
    <div class="hm-reel-body">
      
      <!-- Video / Reel Column -->
      <div class="hm-reel-video-col">
        <div class="hm-reel-card">
          {%- if section.settings.video != blank -%}
            {{ section.settings.video | video_tag: autoplay: true, loop: true, muted: true, controls: false, playsinline: true, class: 'hm-reel-media' }}
          {%- elsif section.settings.video_url != blank -%}
            {%- if section.settings.video_url.type == 'youtube' -%}
              <iframe
                src="https://www.youtube.com/embed/{{ section.settings.video_url.id }}?autoplay=1&mute=1&loop=1&playlist={{ section.settings.video_url.id }}&controls=0&playsinline=1"
                class="hm-reel-media"
                allow="autoplay; encrypted-media"
                allowfullscreen
              ></iframe>
            {%- elsif section.settings.video_url.type == 'vimeo' -%}
              <iframe
                src="https://player.vimeo.com/video/{{ section.settings.video_url.id }}?autoplay=1&muted=1&loop=1&controls=0"
                class="hm-reel-media"
                allow="autoplay; fullscreen"
                allowfullscreen
              ></iframe>
            {%- endif -%}
          {%- elsif section.settings.fallback_image != blank -%}
            <img
              src="{{ section.settings.fallback_image | image_url: width: 800 }}"
              alt="{{ section.settings.fallback_image.alt | default: 'Reel preview' }}"
              class="hm-reel-media"
              loading="lazy"
              width="400"
              height="711"
            >
          {%- else -%}
            <div class="hm-reel-placeholder">
              {{ 'lifestyle-1' | placeholder_svg_tag: 'placeholder-svg' }}
            </div>
          {%- endif -%}

          {%- if section.settings.badge_text != blank -%}
            <span class="hm-reel-badge">{{ section.settings.badge_text | escape }}</span>
          {%- endif -%}
        </div>
      </div>

      <!-- Products Grid -->
      <div class="hm-reel-products-col">
        <div class="hm-product-track">
          {%- if collection != blank and collection.products_count > 0 -%}
            {%- for product in collection.products limit: 6 -%}
              <div class="hm-product-card {% if forloop.index > 4 %}hm-product-desktop-only{% endif %}">
                <a href="{{ product.url }}" class="hm-product-link">
                  <div class="hm-product-image-wrapper">
                    {%- if product.featured_image != blank -%}
                      <img
                        src="{{ product.featured_image | image_url: width: 400 }}"
                        alt="{{ product.title | escape }}"
                        loading="lazy"
                        class="hm-product-image"
                        width="250"
                        height="250"
                      >
                    {%- else -%}
                      {{ 'product-1' | placeholder_svg_tag: 'placeholder-svg' }}
                    {%- endif -%}
                  </div>
                  
                  <div class="hm-product-meta">
                    <h3 class="hm-product-title">{{ product.title }}</h3>
                    <div class="hm-product-pricing">
                      <span class="hm-product-price">{{ product.price | money }}</span>
                      {%- if product.compare_at_price > product.price -%}
                        <span class="hm-product-compare-price">{{ product.compare_at_price | money }}</span>
                      {%- endif -%}
                    </div>
                  </div>
                </a>

                {%- if product.available -%}
                  <form method="post" action="{{ routes.cart_add_url }}" class="hm-quick-add-form">
                    <input type="hidden" name="id" value="{{ product.selected_or_first_available_variant.id }}">
                    <button type="submit" class="hm-quick-add-btn">
                      <span>Add to Cart</span>
                    </button>
                  </form>
                {%- else -%}
                  <button type="button" class="hm-quick-add-btn" disabled>
                    <span>Sold Out</span>
                  </button>
                {%- endif -%}
              </div>
            {%- endfor -%}
          {%- else -%}
            {%- for i in (1..6) -%}
              <div class="hm-product-card {% if i > 4 %}hm-product-desktop-only{% endif %}">
                <div class="hm-product-image-wrapper">
                  {{ 'product-' | append: i | placeholder_svg_tag: 'placeholder-svg' }}
                </div>
                <div class="hm-product-meta">
                  <h3 class="hm-product-title">Sample Product {{ i }}</h3>
                  <div class="hm-product-pricing">
                    <span class="hm-product-price">{{ 1999 | money }}</span>
                  </div>
                </div>
              </div>
            {%- endfor -%}
          {%- endif -%}
        </div>
      </div>

    </div>
  </div>
</div>

<style>
  .section-{{ section.id }}-padding {
    padding-top: {{ section.settings.padding_top }}px;
    padding-bottom: {{ section.settings.padding_bottom }}px;
  }

  .hm-reel-showcase {
    width: 100%;
    position: relative;
    box-sizing: border-box;
  }

  .hm-reel-container {
    max-width: {{ section.settings.container_width }}px;
    margin: 0 auto;
    padding: 0 20px;
    box-sizing: border-box;
  }

  /* Header Styles */
  .hm-reel-header {
    display: flex;
    justify-content: space-between;
    align-items: flex-end;
    gap: 16px;
    margin-bottom: 24px;
    position: relative;
    width: 100%;
  }

  .hm-reel-title-wrap {
    display: flex !important;
    flex-direction: column !important;
    align-items: flex-start !important;
    gap: 6px;
  }

  .hm-reel-subheading-pill {
    display: inline-flex;
    align-items: center;
    gap: 8px;
    background: rgba(234, 179, 8, 0.12);
    border: 1px solid rgba(234, 179, 8, 0.35);
    padding: 4px 12px;
    border-radius: 24px;
    width: fit-content;
  }

  .hm-pulse-dot {
    width: 7px;
    height: 7px;
    background-color: #eab308;
    border-radius: 50%;
    box-shadow: 0 0 0 0 rgba(234, 179, 8, 0.7);
    animation: hmPulse 1.8s infinite;
  }

  @keyframes hmPulse {
    0% {
      transform: scale(0.95);
      box-shadow: 0 0 0 0 rgba(234, 179, 8, 0.7);
    }
    70% {
      transform: scale(1);
      box-shadow: 0 0 0 6px rgba(234, 179, 8, 0);
    }
    100% {
      transform: scale(0.95);
      box-shadow: 0 0 0 0 rgba(234, 179, 8, 0);
    }
  }

  .hm-reel-subheading {
    font-size: 0.75rem;
    font-weight: 700;
    text-transform: uppercase;
    letter-spacing: 0.08em;
    color: inherit;
    line-height: 1;
  }

  .hm-reel-heading {
    margin: 0;
    font-size: 1.65rem;
    font-weight: 800;
    line-height: 1.2;
    display: inline-flex;
    flex-direction: column;
    align-items: flex-start;
    width: fit-content;
  }

  .hm-heading-underline {
    display: block;
    width: 100%;
    height: 3.5px;
    background: #eab308;
    border-radius: 2px;
    margin-top: 6px;
  }

  .hm-reel-view-all {
    display: inline-flex;
    align-items: center;
    gap: 6px;
    font-weight: 600;
    font-size: 0.88rem;
    text-decoration: none;
    color: inherit;
    padding: 7px 14px;
    border-radius: 20px;
    background: rgba(0, 0, 0, 0.04);
    white-space: nowrap !important;
    flex-shrink: 0 !important;
    transition: all 0.2s ease;
  }

  .hm-reel-view-all:hover {
    background: rgba(0, 0, 0, 0.08);
  }

  .hm-arrow-icon {
    flex-shrink: 0;
    transition: transform 0.2s ease;
  }

  .hm-reel-view-all:hover .hm-arrow-icon {
    transform: translateX(3px);
  }

  /* Video Element Styles */
  .hm-reel-card {
    position: relative;
    border-radius: 16px;
    overflow: hidden;
    background: #000;
    box-shadow: 0 8px 24px rgba(0, 0, 0, 0.08);
  }

  .hm-reel-media {
    width: 100%;
    height: 100%;
    object-fit: cover;
    display: block;
    border: none;
  }

  .hm-reel-placeholder {
    width: 100%;
    height: 100%;
    background: #e2e8f0;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .hm-reel-badge {
    position: absolute;
    top: 14px;
    left: 14px;
    background: rgba(0, 0, 0, 0.75);
    color: #fff;
    backdrop-filter: blur(6px);
    font-size: 0.68rem;
    font-weight: 700;
    padding: 5px 12px;
    border-radius: 20px;
    text-transform: uppercase;
    letter-spacing: 0.06em;
  }

  /* Product Card Styles */
  .hm-product-card {
    background: #ffffff;
    border-radius: 12px;
    padding: 10px;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.04);
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    transition: transform 0.2s ease, box-shadow 0.2s ease;
    border: 1px solid rgba(0, 0, 0, 0.04);
  }

  .hm-product-card:hover {
    transform: translateY(-2px);
    box-shadow: 0 6px 16px rgba(0, 0, 0, 0.08);
  }

  .hm-product-link {
    text-decoration: none;
    color: inherit;
    display: flex;
    flex-direction: column;
    flex-grow: 1;
  }

  .hm-product-image-wrapper {
    width: 100%;
    aspect-ratio: 1 / 1;
    border-radius: 8px;
    overflow: hidden;
    background: #f8fafc;
    margin-bottom: 8px;
    position: relative;
  }

  .hm-product-image {
    width: 100%;
    height: 100%;
    object-fit: cover !important;
    display: block;
    transition: transform 0.3s ease;
  }

  .hm-product-card:hover .hm-product-image {
    transform: scale(1.04);
  }

  .hm-product-title {
    font-size: 0.8rem;
    font-weight: 600;
    line-height: 1.25;
    margin: 0 0 4px 0;
    display: -webkit-box;
    -webkit-line-clamp: 2;
    -webkit-box-orient: vertical;
    overflow: hidden;
    min-height: 2.2em;
  }

  .hm-product-pricing {
    display: flex;
    align-items: center;
    gap: 6px;
    margin-bottom: 8px;
  }

  .hm-product-price {
    font-size: 0.88rem;
    font-weight: 700;
  }

  .hm-product-compare-price {
    font-size: 0.78rem;
    opacity: 0.5;
    text-decoration: line-through;
  }

  .hm-quick-add-btn {
    width: 100%;
    padding: 7px 8px;
    border-radius: 6px;
    border: none;
    background: #111827;
    color: #ffffff;
    font-size: 0.76rem;
    font-weight: 600;
    cursor: pointer;
    transition: background-color 0.2s ease;
  }

  .hm-quick-add-btn:hover:not(:disabled) {
    background: #374151;
  }

  /* Desktop Layout (750px and above) */
  @media screen and (min-width: 750px) {
    .hm-reel-body {
      display: grid;
      grid-template-columns: minmax(280px, 320px) 1fr;
      gap: 24px;
      align-items: stretch;
    }

    .hm-reel-video-col {
      width: 100%;
      display: flex;
    }

    .hm-reel-card {
      width: 100%;
      height: 100%;
      min-height: 100%;
    }

    .hm-product-track {
      display: grid;
      grid-template-columns: repeat(3, minmax(0, 1fr));
      gap: 14px;
      height: 100%;
    }

    .hm-product-desktop-only {
      display: flex !important;
    }
  }

  /* Mobile Layout (Under 750px) */
  @media screen and (max-width: 749px) {
    .hm-reel-heading {
      font-size: 1.35rem;
    }

    .hm-reel-view-all {
      padding: 6px 12px;
      font-size: 0.82rem;
    }

    .hm-reel-body {
      display: flex;
      flex-direction: column;
      gap: 18px;
    }

    .hm-reel-video-col {
      width: 100%;
    }

    .hm-reel-card {
      width: 100%;
      aspect-ratio: 9 / 16;
      max-height: 72vh;
    }

    .hm-product-track {
      display: grid;
      grid-template-columns: repeat(2, 1fr);
      gap: 10px;
    }

    .hm-product-desktop-only {
      display: none !important;
    }

    .hm-product-card {
      padding: 8px;
    }

    .hm-product-title {
      font-size: 0.76rem;
    }

    .hm-quick-add-btn {
      padding: 6px 6px;
      font-size: 0.72rem;
    }
  }
</style>

{% schema %}
{
  "name": "Reel & Collection",
  "tag": "section",
  "class": "section",
  "settings": [
    {
      "type": "header",
      "content": "Collection Settings"
    },
    {
      "type": "collection",
      "id": "selected_collection",
      "label": "Collection"
    },
    {
      "type": "text",
      "id": "heading",
      "label": "Heading",
      "info": "Defaults to collection title if left blank"
    },
    {
      "type": "text",
      "id": "subheading",
      "label": "Subheading Badge",
      "default": "BUILT FOR FUN"
    },
    {
      "type": "header",
      "content": "Reel Video Settings"
    },
    {
      "type": "video",
      "id": "video",
      "label": "Shopify Hosted Video (MP4)"
    },
    {
      "type": "video_url",
      "id": "video_url",
      "label": "External Video URL",
      "accept": ["youtube", "vimeo"],
      "info": "Used if no Shopify video file is uploaded"
    },
    {
      "type": "image_picker",
      "id": "fallback_image",
      "label": "Fallback Poster Image"
    },
    {
      "type": "text",
      "id": "badge_text",
      "label": "Reel Tag / Badge",
      "default": "Watch & Shop"
    },
    {
      "type": "header",
      "content": "Appearance & Spacing"
    },
    {
      "type": "color_scheme",
      "id": "color_scheme",
      "label": "Color Scheme",
      "default": "scheme-1"
    },
    {
      "type": "range",
      "id": "container_width",
      "min": 1000,
      "max": 1400,
      "step": 20,
      "unit": "px",
      "label": "Container max width",
      "default": 1200
    },
    {
      "type": "range",
      "id": "padding_top",
      "min": 0,
      "max": 100,
      "step": 4,
      "unit": "px",
      "label": "Top padding",
      "default": 32
    },
    {
      "type": "range",
      "id": "padding_bottom",
      "min": 0,
      "max": 100,
      "step": 4,
      "unit": "px",
      "label": "Bottom padding",
      "default": 32
    }
  ],
  "presets": [
    {
      "name": "Reel & Collection"
    }
  ]
}
{% endschema %}

```

---

### Step 2: Add and Configure in Theme Customizer

1. Go to **Shopify Admin** > **Online Store** > **Themes** and click **Customize**.
2. Navigate to your **Home page** (or any custom landing page template).
3. In the left-hand section panel, click **Add section**.
4. Search for and select **Reel & Collection**.
5. In the section inspector panel on the right:
* **Select Collection:** Choose any active product collection.
* **Reel Video:** Upload a vertical 9:16 `.mp4` video file (ideal resolution: 1080×1920) or paste a video link.
* **Heading & Subheading:** Customize the headline or leave blank to inherit the collection's title automatically.


6. Click **Save** in the top right corner.
