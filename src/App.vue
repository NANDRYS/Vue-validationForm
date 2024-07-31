
<template>
<div class="container">
  
  
  <div class="field">
    <label class="label">
      Имя
    </label>
    <div class="control">
      <input
      v-model="form.name"
      @focus="v.$reset()"
      :class="{'is-danger': !!validate({prop: 'name'})}"
      class="input"
      type="text"
      >
      <p v-if="!!validate({prop: 'name'})"
      class="help  is-danger">
        {{ validate({prop:'name'}) }}
      </p>
    </div>
  </div>


  <div class="field">
    <label class="label">
      Почта
    </label>
    <div class="control">
      <input
      v-model="form.email"
      :class="{'is-danger': !!validate({prop: 'email'})}"
      @focus="v.$reset()"

      class="input"
      type="text"
      >
      <p v-if="!!validate({prop: 'email'})"
      class="help  is-danger">
        {{ validate({prop:'email'}) }}
      </p>
    </div>
  </div>

  <div class="field">
    <label class="label">
      Номер телефона
    </label>
    <div class="control">
      <input
      v-model="form.phone"
      :class="{'is-danger': !!validate({prop: 'phone'})}"
      v-maska:data-maska="'+7 (###) - ### - ## - ##'"
      @focus="v.$reset()"

      class="input"
      type="text"
      >
      <p v-if="!!validate({prop: 'phone'})"
      class="help  is-danger">
        {{ validate({prop:'phone'}) }}
      </p>
    </div>
  </div>

  <div class="field">
    <label class="label">
      Сообщение
    </label>
    <div class="control">
      <textarea
      v-model="form.textarea"
      class="textarea"
      :class="{'is-danger': !!validate({prop: 'textarea'})}"
      @focus="v.$reset()"

      type="text"
      ></textarea>
      <p v-if="!!validate({prop: 'textarea'})"
      class="help  is-danger">
        {{ validate({prop:'textarea'}) }}
      </p>
    </div>
  </div>

  <div class="field">
    <label class="label">
      Пароль
    </label>
    <div class="control">
      <input
      v-model="form.password"
      class="input"
      :class="{'is-danger': !!validate({prop: 'password'})}"
      @focus="v.$reset()"

      type="password"
      />
      <p v-if="!!validate({prop: 'password'})"
      class="help  is-danger">
        {{ validate({prop:'password'}) }}
      </p>
    </div>
  </div>

<div class="field">
    <label class="label">
      Повторите пароль
    </label>
    <div class="control">
      <input
      v-model="form.currentPassword"
      class="input"
      :class="{'is-danger': !!validate({prop: 'currentPassword'})}"
      @focus="v.$reset()"

      type="password"
      >
      <p v-if="!!validate({prop: 'currentPassword'})"
      class="help  is-danger">
        {{ validate({prop:'currentPassword'}) }}
      </p>
    </div>
  </div>

  <div class="field">
    <div class="control">
    <label class="label"
      :class="{'has-text-danger': !!validate({prop: 'checkbox'})}"
      @focus="v.$reset()"
      >
      <input
      v-model="form.checkbox"
      type="checkbox"
      > Я соглашаюсь с <a href=""> правилами сообщества </a> 
    </label>
    </div>
  </div>

  <div class="control" mx-auto mt-3>
    <button 
    @click="onsubmit"
    :class="{'is-loading': form.pending}"
    :disabled="form.pending"
    class="button is-link"
    >
      Отправить
    </button>
  </div>

</div>
</template>


<script>
import { reactive, computed } from 'vue';
import { required, email, requiredPhone, minLength, maxLength, sameAs } from './utils/i18n-validators';
import { vMaska } from 'Maska/vue'
import useVuelidate from '@vuelidate/core'; 

export default { 
  //data
  setup(){
    const form = reactive({
      name: null,
      email: null,
      phone: null,
      textarea: null,
      password: null,
      currentPassword: null,
      checkbox: null,
      pending: null
    })
    //computed

    const rules =computed (()=>({
      name: {
        required,
        minLength: minLength(3)
      },
      email:{
        required,
        email
      },
      phone: {
        required,
        requiredPhone:requiredPhone(24)
      },
      textarea:{
        required,
        minLength: minLength(5)
      },
      password:{
        required,
        minLength:minLength(5),
        maxLength:maxLength(15)
      },
      currentPassword:{
        required,
        minLength:minLength(5),
        maxLength:maxLength(15),
        sameAs:sameAs(form.password)
      },
      checkbox:{
        required,
        sameAs: sameAs(true)
      }
    }))


    const v = useVuelidate(rules, form)
    //methods
    const onsubmit = async () =>{
      v.value.$touch();

      if (form.pending) return;

      if (await v.value.$validate()) {
        form.panding = true

        try{
          const payload = { 
            name: form.name,
            email: form.email,
            phone: form.phone,
            textarea: form.textarea,
            password: form.password,
            currentPassword: form.currentPassword,
            checkbox: form.checkbox,            
          }
          setTimeout(() => {
              console.log(payload);
              console.log('Запрос отправлен');

              resetForm()
            }, 2500);
        }catch(err){
          console.log(err);
        }
      }

    }
    

    const validate = ({prop}) =>{
      const error = v.value.$errors.find((el) => el.$property === prop)
      return error && error.$message
    }


    const resetForm = () => {
      Object.keys(form).forEach((key) => {
        form[key] = null;
      })

      v.value.$reset();
    }

    return{ v, form, onsubmit, validate }
  },
  directives:{
    maska: vMaska
  }
}
</script>