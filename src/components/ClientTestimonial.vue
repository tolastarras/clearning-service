<template lang="html">
  <div class="testimonial-quote group">
    <blockquote>
      <p>{{ testimonial.quote }}</p>
    </blockquote>
    <cite>
      <span class="font-weight-bold">
        {{ testimonial.client.name }}
      </span>, {{ testimonial.client.city }}
    </cite>
  </div>
</template>

<script>
export default {
  name: 'ClientTestimonial',
  props: {
    testimonial: {
      type: Object,
      required: true
    }
  },
  methods: {
    lastChild (i) {
      return (i + 1) === this.testimonials.length ? 'last' : ''
    }
  }
}
</script>

<style lang="scss" scoped>
.testimonial-quote {
  font-size: $font-16;
  width: 100%;

  &.group {
    padding-bottom: 3em;

    &.last {
      padding-bottom: 0;
    }
  }

  cite {
    display: block;
    color: $color-07;
    font-size: $font-16;
  }

  blockquote {
    font-family: Georgia, serif;
    font-size: $font-24;
    font-style: italic;
    line-height: 1.4 !important;
    position: relative;

    p {
      color: $color-07;

      &:first-child:before {
        content: '\201C';
        color: $color-06;
        font-size: $font-170;
        font-weight: 700;
        opacity: 0.2;
        position: absolute;
        top: -4.5rem;
        left: -2.5rem;
      }
    }
  }
}

@media (max-width: 1024px) {
  .container {
    padding-right: 1.8em !important;

    h1 {
      margin: 2rem 0 3.5rem;
    }

    .testimonial-quote {
      padding-left: 1.5rem;
    }
  }
}
</style>
