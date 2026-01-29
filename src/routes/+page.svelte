<script>
  let currentSlide = 0;
  let previousSlide = 0;
  let isTransitioning = false;
  let selectedProject = null;
  let direction = 'next'; // Track direction: 'next' or 'prev'
  
  const projects = [
    {
      title: "Movie Recommendation Website", 
      description: "A website that allows users to keep track of movies they've watched and receive personalized movie recommendations based on their viewing history.",  
      tech: ["SveltKit", "TypeScript", "TMDB API", "PostgreSQL"],
      details: {
        github: "https://github.com/baseln-dev/MovieRec",
        demo: "https://movierec.rreed.dev",
        features: ["TMDB API integration", "Content-based filtering", "Database indexing", "User authentication"],
        challenges: "optimizing recommendation algorithms for performance and accuracy"
      }
    },
    {
      title: "Spotify Playlist Sorter",
      description: "A sorting tool which connects to a user's Spotify account and allows them to create playlists by splitting an existing playlist into genres.",
      tech: ["Svelte", "TailwindCSS", "TypeScript", "Spotify API"],
      details: {
        github: "https://playlist-sorter.rreed.dev",
        demo: "https://playlist-sorter.rreed.dev", 
        features: ["OAuth Integration", "Genre Classification", "Automatic Playlist Creation", "Bulk Track Management"],
        challenges: "Working with Spotify's API rate limits and implementing reliable genre detection"
      }
    },
    {
      title: "Task Organisation App",
      description: "A mobile application designed to help users organize and manage their tasks efficiently with features like categorization, deadlines, and reminders.",
      tech: [".NET MAUI", "C#", "SQLite"],
      details: {
        features: ["Task Categorization", "Deadline Management", "Reminders", "Synchronization"],
        challenges: "managing data synchronization across devices and implementing a user-friendly interface",
      }
    }
  ];

  function viewProjectDetails(project) {
    selectedProject = project;
    // Pause autoplay when viewing details
    stopAutoplay();
  }

  function closeProjectDetails() {
    selectedProject = null;
    // Resume autoplay when closing details
    startAutoplay();
  }

  function nextSlide() {
    if (isTransitioning) return;
    isTransitioning = true;
    direction = 'next';
    previousSlide = currentSlide;
    currentSlide = (currentSlide + 1) % projects.length;
    setTimeout(() => {
      isTransitioning = false;
    }, 300);
  }

  function prevSlide() {
    if (isTransitioning) return;
    isTransitioning = true;
    direction = 'prev';
    previousSlide = currentSlide;
    currentSlide = currentSlide === 0 ? projects.length - 1 : currentSlide - 1;
    setTimeout(() => {
      isTransitioning = false;
    }, 300);
  }

  function goToSlide(index) {
    if (isTransitioning || index === currentSlide) return;
    isTransitioning = true;
    direction = index > currentSlide ? 'next' : 'prev';
    previousSlide = currentSlide;
    currentSlide = index;
    setTimeout(() => {
      isTransitioning = false;
    }, 300);
  }

  // Auto-advance slideshow every 8 seconds (increased from 5)
  let interval;
  
  function startAutoplay() {
    interval = setInterval(nextSlide, 8000);
  }
  
  function stopAutoplay() {
    clearInterval(interval);
  }
  
  // Start autoplay when component mounts
  import { onMount, onDestroy } from 'svelte';
  
  onMount(() => {
    startAutoplay();
  });
  
  onDestroy(() => {
    stopAutoplay();
  });
</script>

<svelte:head>
  <title>Your Name - CS Student Portfolio</title>
  <meta name="description" content="Computer Science student portfolio showcasing programming projects and technical skills" />
</svelte:head>

<div class="container">
  <section class="page-section hero-section">
    <header>
      <h1>Rufus Reed</h1>
      <p class="tagline">Computer Science Student | Aspiring Software Engineer</p>
    </header>
  </section>

  <section class="page-section about-section">
    <div class="section-content">
      <h2>About Me</h2>
      <p>
        Computer Science student working on practical software projects alongside my degree. Interests include full-stack development, game development, and mobile applications, with a focus on clear design and strong technical fundamentals.
      </p>
    </div>
  </section>

  <section class="page-section projects-section">
    <div class="section-content">
      <h2>A Few Projects I've Done</h2>
      
      <div class="slideshow-container">
        <div class="slideshow-wrapper">
        {#each projects as project, index}
          <div 
            class="slide" 
            class:active={currentSlide === index}
            class:exiting-next={isTransitioning && previousSlide === index && direction === 'next'}
            class:exiting-prev={isTransitioning && previousSlide === index && direction === 'prev'}
            class:entering-next={isTransitioning && currentSlide === index && direction === 'next'}
            class:entering-prev={isTransitioning && currentSlide === index && direction === 'prev'}
          >
            <div class="project">
              <h3>{project.title}</h3>
              <p>{project.description}</p>
              <div class="tech-stack">
                {#each project.tech as tech}
                  <span>{tech}</span>
                {/each}
                <button 
                  class="details-btn" 
                  on:click={() => viewProjectDetails(project)}
                  on:click|stopPropagation
                >
                  Details
                </button>
              </div>
            </div>
          </div>
        {/each}
      </div>

      <div class="slideshow-controls">
        <button class="prev-btn" on:click={prevSlide}>❮</button>
        <div class="slide-indicators">
          {#each projects as _, index}
            <button 
              class="indicator" 
              class:active={currentSlide === index}
              on:click={() => goToSlide(index)}
            >
              {index + 1}
            </button>
          {/each}
        </div>
        <button class="next-btn" on:click={nextSlide}>❯</button>
      </div>
      </div>
    </div>
  </section>

  <!-- Project Details Modal Overlay -->
  {#if selectedProject}
    <div 
      class="project-details-overlay" 
      on:click={closeProjectDetails}
      on:keydown={(e) => e.key === 'Escape' && closeProjectDetails()}
      role="button"
      tabindex="0"
      aria-label="Close modal"
    >
      <div
        id="project-details" 
        class="project-details-modal" 
        on:click|stopPropagation
        on:keydown|stopPropagation
        role="dialog"
        aria-modal="true"
        aria-labelledby="modal-title"
        tabindex="-1"
      >
      <div class="details-header">
        <h2 id="modal-title">{selectedProject.title}</h2>
        <button class="close-btn" on:click={closeProjectDetails}>×</button>
      </div>
      
      <div class="details-content">
        <div class="details-overview">
          <h3>Overview</h3>
          <p>{selectedProject.description}</p>
          
          <h3>Key Features</h3>
          <ul class="features-list">
            {#each selectedProject.details.features as feature}
              <li>{feature}</li>
            {/each}
          </ul>
          
          <h3>Technical Challenges</h3>
          <p class="challenges">{selectedProject.details.challenges}</p>
        </div>
        
        <div class="details-sidebar">
          <div class="tech-used">
            <h3>Technologies Used</h3>
            <div class="tech-grid">
              {#each selectedProject.tech as tech}
                <span class="tech-tag">{tech}</span>
              {/each}
            </div>
          </div>
          
          <div class="project-links">
            <h3>Project Links</h3>
            <div class="links-grid">
              <a href={selectedProject.details.github} target="_blank" rel="noopener noreferrer" class="project-link github">
                <span>GitHub Repository</span>
                <svg width="20" height="20" viewBox="0 0 24 24" fill="currentColor">
                  <path d="M12 0C5.37 0 0 5.37 0 12c0 5.31 3.435 9.795 8.205 11.385.6.105.825-.255.825-.57 0-.285-.015-1.23-.015-2.235-3.015.555-3.795-.735-4.035-1.41-.135-.345-.72-1.41-1.23-1.695-.42-.225-1.02-.78-.015-.795.945-.015 1.62.87 1.845 1.23 1.08 1.815 2.805 1.305 3.495.99.105-.78.42-1.305.765-1.605-2.67-.3-5.46-1.335-5.46-5.925 0-1.305.465-2.385 1.23-3.225-.12-.3-.54-1.53.12-3.18 0 0 1.005-.315 3.3 1.23.96-.27 1.98-.405 3-.405s2.04.135 3 .405c2.295-1.56 3.3-1.23 3.3-1.23.66 1.65.24 2.88.12 3.18.765.84 1.23 1.905 1.23 3.225 0 4.605-2.805 5.625-5.475 5.925.435.375.81 1.095.81 2.22 0 1.605-.015 2.895-.015 3.3 0 .315.225.69.825.57A12.02 12.02 0 0024 12c0-6.63-5.37-12-12-12z"/>
                </svg>
              </a>
              <a href={selectedProject.details.demo} target="_blank" rel="noopener noreferrer" class="project-link demo">
                <span>Live Demo</span>
                <svg width="20" height="20" viewBox="0 0 24 24" fill="currentColor">
                  <path d="M14 3v2h3.59l-9.83 9.83 1.41 1.41L19 6.41V10h2V3m-2 16H5V5h7V3H5a2 2 0 0 0-2 2v14a2 2 0 0 0 2 2h14a2 2 0 0 0 2-2v-7h-2v7z"/>
                </svg>
              </a>
            </div>
          </div>
        </div>
      </div>
      </div>
    </div>
  {/if}

  <section class="page-section skills-section">
    <div class="section-content">
      <h2>Technical Skills</h2>
      <div class="skills-grid">
        <div class="skill-category">
          <h3>Programming Languages</h3>
          <p>JavaScript, Typescript, Java, C# and C++</p>
        </div>
        <div class="skill-category">
          <h3>Web Development</h3>
          <p>React, Node.js, SvelteKit, TailwindCSS, Bootstrap</p>
        </div>
        <div class="skill-category">
          <h3>Tools & Technologies</h3>
          <p>Git, PostgreSQL</p>
        </div>
      </div>
    </div>
  </section>

  <section class="page-section contact-section">
    <div class="section-content">
      <h2>Let's Connect</h2>
      <p>
        Interested in collaborating on a project or discussing opportunities? <br/>
        I'm always open to connecting with fellow developers and potential employers.
      </p>
      <div class="contact-links">
        <a href="mailto:rufus_reed@hotmail.com">Email</a>
        <a href="https://www.linkedin.com/in/rufus-reed-b957403a4/">LinkedIn</a>
        <a href="https://github.com/baseln-dev">GitHub</a>
      </div>
    </div>
  </section>
</div>

