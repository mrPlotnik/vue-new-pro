<template lang="pug">
.col.s12.m6
  div
    .page-subtitle
      h4 Создать
    form(@submit.prevent="submitHandler")
      .input-field
        input#name(
          type="text"
          v-model.trim="title"
          :class="{invalid: $v.title.$dirty && !$v.title.required}"
        )
        label(for="name") Название
        span.helper-text.invalid(
          v-if="$v.title.$dirty && !$v.title.required"
        ) Введите название категории

      .input-field
        input#limit(
          type="number"
          v-model.number="limit"
          :class="{invalid: ($v.limit.$dirty && !$v.limit.minValue) || !$v.limit.required}"
        )
        label(for="limit") Лимит
        span.helper-text.invalid(
          v-if="($v.limit.$dirty && !$v.limit.minValue)"
        ) Минимальное значение {{ $v.limit.$params.minValue.min }}
        span.helper-text.invalid(
          v-else-if="!$v.limit.required"
        ) Введите значение

      button.btn.waves-effect.waves-light(type="submit")
        | Создать
        i.material-icons.right send
</template>

<script>

// Импортируем два валидатора
import M from 'materialize-css'
import { required, minValue } from 'vuelidate/lib/validators'

export default {
  // Делаем две модели для инпутов
  data: () => ({
    title: '',
    limit: 100
  }),
  validations: {
    title: { required },
    limit: { required, minValue: minValue(100) }
  },
  mounted () {
    M.updateTextFields()
  },
  methods: {
    async submitHandler () {
      if (this.$v.$invalid) {
        this.$v.$touch()
        this.$message('Форма невалидна')
        return
      }
      try {
        const category = await this.$store.dispatch('createCategory', {
          title: this.title,
          limit: this.limit
        })
        this.title = ''
        this.limit = 100
        this.$v.$reset()
        this.$message('Категория была создана')
        this.$emit('created', category)
      } catch (e) {}
    }
  }
}
</script>
