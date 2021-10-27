<template>
  <div :class="`px-2 text-white rounded-full bg-${statusColor}-500 shadow-inner`">
    {{ statusText }}
  </div>
</template>

<script>
export default {
  props: {
    status: {
      type: String,
      required: true,
    }
  },

  setup(props) {
    const statuses = {
      'approved': ['Aprovado', 'success'],
      'rejected': ['Rejeitado', 'critical'],
      'expired': ['Expirado', 'warning']
    }

    const fallback = [props.status?.capitalize()||'Desconhecido', 'gray']
    const status = statuses[props.status]||fallback

    return {
      statusText: status[0],
      statusColor: status[1]
    }
  }
}
</script>
