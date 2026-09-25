---
layout: default
title: Contact
permalink: /contact/
lang: en
---

<div class="portfolio-contact">
  <section class="contact-hero">
    <div class="portfolio-container contact-hero__grid">
      <div>
        <p class="portfolio-eyebrow">Contact</p>
        <h1>Let's discuss your project.</h1>
        <p class="contact-hero__lead">Looking for experienced engineering support for an AEM, Java or full-stack web project? Tell me a little about what you're building and where you need help.</p>
        <div class="contact-availability"><span aria-hidden="true"></span><div><strong>Available for freelance &amp; B2B projects</strong><small>Remote collaboration across Europe</small></div></div>
      </div>
      <div class="contact-details">
        <p class="portfolio-eyebrow">Get in touch</p>
        <h2>Start a conversation.</h2>
        <p>The easiest way to reach me is by email. Include a short description of your project, the technologies involved and the kind of support you're looking for.</p>
        <a class="contact-email" href="mailto:{{ site.email }}"><span>Email</span><strong>{{ site.email }}</strong><b aria-hidden="true">→</b></a>
        <div class="contact-social">{% if site.github_username %}<a href="https://github.com/{{ site.github_username }}" rel="noopener" target="_blank">GitHub ↗</a>{% endif %}{% if site.linkedin_username %}<a href="https://www.linkedin.com/in/{{ site.linkedin_username }}" rel="noopener" target="_blank">LinkedIn ↗</a>{% endif %}</div>
      </div>
    </div>
  </section>

  <section class="portfolio-section contact-project">
    <div class="portfolio-container">
      <div class="portfolio-section__heading"><p class="portfolio-eyebrow">Project Enquiries</p><h2>Helpful details to include.</h2><p>You don't need a complete specification. A few details are enough to start the conversation.</p></div>
      <div class="contact-project__grid">
        <article><span>01</span><h3>Your project</h3><p>A short description of the application, platform or problem you're working on.</p></article>
        <article><span>02</span><h3>Technology</h3><p>The current stack — for example AEM, Java, Spring Boot, React, Next.js or related systems.</p></article>
        <article><span>03</span><h3>Support needed</h3><p>The area where you need engineering help, from implementation and integration to troubleshooting or modernization.</p></article>
        <article><span>04</span><h3>Timing</h3><p>Your expected start date, approximate duration and whether the work is full-time or part-time.</p></article>
      </div>
    </div>
  </section>

  <section class="contact-focus">
    <div class="portfolio-container contact-focus__grid">
      <div><p class="portfolio-eyebrow">Good Fit</p><h2>Projects I can help with.</h2></div>
      <div class="contact-focus__list">
        <p>Adobe Experience Manager development and customization</p>
        <p>Java and backend application development</p>
        <p>React, Next.js and modern frontend development</p>
        <p>Full-stack integration and enterprise web platforms</p>
        <p>Existing application modernization and technical problem solving</p>
      </div>
    </div>
  </section>

  <section class="contact-cta">
    <div class="portfolio-container contact-cta__inner">
      <p class="portfolio-eyebrow">Ready to Talk?</p>
      <h2>Tell me about your project.</h2>
      <p>Send me an email with a few details and I'll have the context needed to start a useful conversation.</p>
      <a class="portfolio-button portfolio-button--light" href="mailto:{{ site.email }}">Send an Email →</a>
    </div>
  </section>
</div>
