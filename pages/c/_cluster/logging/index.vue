<script>
import { NAME as LOGGING } from '@/config/product/logging';

export default {
  layout: 'plain',

  middleware({ redirect, route, store } ) {
    return redirect({
      name:   'c-cluster-product-resource',
      params: {
        ...route.params,
        product:  LOGGING,
        resource: 'logging.banzaicloud.io.logging',
      }
    });
  }
};
</script>
