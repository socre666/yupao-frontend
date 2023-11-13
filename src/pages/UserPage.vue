<template>
  <van-cell title="昵称" is-link to="/user/edit" :value="user.username" @click="toEdit('username','昵称',user.username)" />
  <van-cell title="账号" :value="user.userAccount" />
  <van-cell title="头像" is-link to="/user/edit" @click="toEdit('avatarUrl','头像',user.avatarUrl)">
    <img style="height: 48px;" :src="user.avatarUrl" />
  </van-cell>
  <van-cell title="性别" is-link :value="user.gender" @click="toEdit('gender','性别',user.gender)"/>
  <van-cell title="电话" is-link to="/user/edit"  :value="user.phone" @click="toEdit('phone','电话',user.phone)" />
  <van-cell title="邮箱" is-link to="/user/edit"  :value="user.email" @click="toEdit('email','邮箱',user.email)"/>
  <van-cell title="星球编号" :value="user.planetCode" />
  <van-cell title="注册时间" :value="user.createTime.toISOString()" />
</template>

<script setup lang="ts">
import {useRouter} from "vue-router";
import {onMounted} from "vue";
import {Toast} from "vant";
import myAxios from "../plugins/myAxios";
const user = {
  id: 1,
  username: "奋斗",
  userAccount: "struggle",
  avatarUrl: "data:image/jpeg;base64,/9j/4AAQSkZJRgABAQAAAQABAAD/2wCEAAkGBwgHBgkIBwgKCgkLDRYPDQwMDRsUFRAWIB0iIiAdHx8kKDQsJCYxJx8fLT0tMTU3Ojo6Iys/RD84QzQ5OjcBCgoKDQwNGg8PGjclHyU3Nzc3Nzc3Nzc3Nzc3Nzc3Nzc3Nzc3Nzc3Nzc3Nzc3Nzc3Nzc3Nzc3Nzc3Nzc3Nzc3N//AABEIAJ4ArAMBIgACEQEDEQH/xAAcAAABBAMBAAAAAAAAAAAAAAAABAUGBwEDCAL/xABCEAABAwMCAwYEBAIIBAcAAAABAgMEAAURBiESMUEHE1FhcYEUIjKRUmKhsRZCFSMkM3KCwfBTktHhCCU0Q5Oisv/EABgBAQEBAQEAAAAAAAAAAAAAAAABAgME/8QAHhEBAQEAAgIDAQAAAAAAAAAAAAERAiESMQMiQRP/2gAMAwEAAhEDEQA/ALtHIelFYTyT6VmgZNTNR34jgurgbs7DZemcRwHUjcIP5dsqHXYbgmuX9eapf1ZfnprmURkHu4jA5NNDkMeJ5mrE7cdesXFKtM2d0OMtrBmvJOUqUk5DY8cEZPmMVS9AUUUUBRRRQFFFFAUUUUBRRRQFFFFAVkc96xRQTrsvYt9xvDMMXOZZrzk/BTY5BQ5t9Cweu2x5HkeldG2dGoI7aWrs7b5hG3xDAUyo+qDxDPoR6VyFbWXpExlqIopkqX/VFJwePpg+OeXnXU3Zfqo6r0y29JIFwinuJiMYPEOSseY39c0Ev8KKKKBNMmsQWe8eLhGNkttqcWryCUgk/aone2NRajYdZee/hyycJLznGFS3UDnuDwtjHmT6cqmnIe1U1256mmPsydP2hK/horaHbpIScJTxHCGifEkg4/0BwFNaifgPXeT/AEPHDFvQotx05JKkDkpRPMnmfWmusnnWKAooooCiiigKKKKAooooCiiigKKKKAooooNjS1tqCmlKQtKgUqScEEciKvvRMoRNS2bUMPgFv1Qx3E5Lf0tTkgknH5iD7k1QFWfoJV1haSXLabXLsxnNurUyMrgSGlIVxEfhUjAJHIHyoOkKK1RZDEuO3JiOtvMOpCm3G1BSVA8iCK20B0OBnyqie3EtWOyW+ysK45M+SudNe6urG2T1xkkAdAnFXt0rlbtav38S6xmTIySqHDAhtOpGUkJJOc8tyVEeVBCjzrFFFAUUUUBRRRQFbFMrShDikKCF54VEbKx4V7bYdcYeebRlDIBWfDJwP1qxezqPpy8aXudq1RPjQ+F3vIzzzqUKbUQN0558txQVoaxTjerU9a5jjSj3zAWQ1JQhQQ8PFORTfg+FBiiiigKK9NoU4tKEJKlqOEpAySfAU/an0pcNM/Bi5BoOSW+NSEnJZP4V+Bxg0EfopTcYbsCa9FfSQtpWDkfY/ak1BkY610H2HwX7U5qKwTFhxCFsvIIHyuIWg4UPUcP2rn0IWUKUlKilOOIgbD1rpfsbdaudpTdc4lIhMW+QgjCstcZCj5FC0Y9KCwmI7MdJSw0hsKOSEJAyfHatlFFAxazmS49hdZtY4rlNIixB4OL/AJj4BKeJR8k1Tuv9INwpmlND2LHev8bz7pG7rhwkuK67BK9ugq+lR0LeadUMrbSQgnpnn+1Rv+h0u9pxvDiARHs7bLSj0Up1zi/TH3NBydJaLMh1o821lJ9jitVOmqWPhtT3djGO6nPIx4YWRTXQFFegM7DJPhS123PItTNxKAI7rimgoLyeIb7jpt+1AgrNenEFCilQII8a8UEs7PbdDut0dh3GVKbjuIAUxETxOSN/pA/Wn/RId0P2gKt94ix0CQ2UNuzW9kA7oXgZxnBGB1PPamTsyuKrfqcFMkxjIYWz3oxlPI7Z2GyTvketbb/dZmonjEtNi7tMVani8lC3JKyASVLcIB5DOABUVcepn7Nqi0uxLs40UEf1MtbBYaSemFrV+1UPDtcKZAehNulV6EpSY6G0lYfQAMjI26Eg1pej3a8cc2Y5IlOK4Q246oqLhUrASknnueVX1oXs+tdr1G9do8UoaitBhgOOFZU6Rh1e/IDJSP8AN5UWyz25tUkpOFDBHMGvNWLqfRK29cX2Ayk92l1t5j/C84kAexUR7VDFwO4vyoLgOG5fcq9l4qsnzsy+EjaqZm3FoLRFbW6yhXJTyQOAfcirl1foA3PT0x6U+FTEtOPLOM8TgBUDnxzt6Eio/YtCF2RdEttpC4V+UGgeSmw0flPkSUirJsc6LcYaoD08SHO6+dpxIQ4EciFYO/gSMVmtTcU12g6YFyh22+soLDztq/tTZTg980gHBHiUBQ9UetVRXTHaVKj2/R16uUtIQZYEeA1jByQocXqQtxX+GuaKsZW12G2i33+26ntM5tKviWWkknmkZUQR5hQB9qtHs1tSoen4C3AWpcZC4UkAbO92tQGfMHOD4H0xXHY9arlpy8yJ7oC4Xe/AzUo3LXElK2nfNJzg+AOeXK92mkNcXdp4eJRUcdSedUe6KKKAFYCUhfFj5sYzWRyo60HIusIUiRqvVMploqZi3F9TyxyQFPKSP1IqNV0RL0smPoPtAmSm8PXGZLkjI5IacUpH6pJ96o6yWN+7fH90SlUSC5MwR9SUYz+mftQN0R4xpLT4SFFpaV8KuRwc4qcNX2xxLddYBTi3z1B5DTLCHHxkfQVqOEBJGRjJ39qgZGTz+9TDs1jsXG/sQXe7Eo5VD74f1S1j6m1jwWnIz0/Si6jkl5uYwwhDa/iG08BVnZSBnh9wMD2ryi3vLta7g2OJlt0NuEc0Ej5c+R3q34vZ3EGqWFohPW2P3zTnw8hxLhSUqBKW+FRK+LhPMAAHc1ut2mWNO65eQ9HA05fHHoSM7ht0LISDnxKVAf4vSppimoLUoFUyIFcUQpdKkjJb32VjwBx9xXS/Z21CmWxEy3o+Bm9yG5jWOMqcJKu9yfq4skhR9OmKgH8KL7OdcRbhIaD2m5XFHfdV8yW0L2wvyzg5q1LNpluzYYgurEZskxHCcrZQTktH8bedx1GT5GmqZ3OzyOq8OTeBjiDodYdQkNFpXU8LYTxHP4iR5VN4rIjsIaSVKCR9Szkk+Jran6RnGcdKzWNVGpFgTK1ZKuLiQEqYiIBzuS26pzH3Sj71DJXZ2mdZL9c2WOK6S7kufDScAlCFqKEeXECf+YVbHvWMeFXUN9qjCMxJfQCVy3TJUk7HJSBj9Ki9gsdzZu4ud47qNGihxSQHASoqG5PgkZPXoKcO0TVLmj7A3cmYqJS1SUM90pRGQrJOMddqp3tE7U7nfITtmYt67WwraUFL4nFjH0cvlH7/AHpmkuGjtZ1t/Ft97uIv/wArhkpjj/iH+ZZ9enl61u0/o9N77KrtdozeZ0GcVoI5raShJWn7Kz7VXx510z2Fw2x2bshaQUSn31LHiM8H7JrbKQ6It6WLW1N2/t0OKpQ80shNSQcttqS2qGLda4cIK4xGYQ1xePCkDP6UqoCiiigByooHKigbNSwF3WwT7a0QkzGFMcX4Qv5SfYEmo25paBZLzElsBpi3t2dy3OFxQHLhKCSeZI48+nnT7cbtJM4261MIekhILjjp/q2s9COZPXGRzG9Yi2xCZIkXd9MyZgJR3gHC3k8kJ5DOOfXFTV8VAW3QyQiZbniXJs23syLesJ2SsrBUCRnGBseu48RUPMS4WbUIipSW7hDlBKenC4lQ4d/XFdcXm3/HRB3BS3KYPeR3PwrHj5HkfWoFa9Mw9Qayv86bEwxMtzTMhJ5okcRSrB6KT3YPqakpic2G5Jutki3NTJQ440e9Rw/MhYJC0eygoY8q9ptjUm0CFPa7wLHE6kn+YniJB6EHkR4UgtHfxlyLWt/hkAJWh0oyFqAGTj8wAUR4le+xNOCJj5fMN5kMSVtFTTmC40vHPwORkbHGenXEqlao7bsZUeSlL6FJ4VhxIIWPMcqIkZqJHRHYTwtNjhQknPCOg36V7aDgQA6pKl43KU8IJ8hk4+9e8ioDlRRmioCiivDzrbLanHVBKEjJJoIrrRhq43C0Q3EBYZcVJAPReOBH/wCl/aqw1PppUrR+oNRlrBlzC8ySP/aQrhSR5EAn3q0Lc2q63uTJdJAUggAfyI5JA89yfUmnq82pmdYJdrS2EtOxlsoSkbJ+XAx+lJW7105I01Z13+8sWtlYQ/ICg0TyKwkkA+uMV0P2Eur/AIG+DdQpt6DNeYcbWMFCshWCP81QQaOds9l0hqu2tud/GabelNAbrHEVqwPHgKv+XxxV3QrYxDuUybEwlM7hW8lOMKWBjjHmRjPoK6uRwooooCiiigBypvvtyFrgF4DjeWeBlv8AGs/6DcnyBpw5A1HICTfbsq5ubwo+WoieYX4r9zy8gPE1L0sjbbmXLTC+IkNuPrfJXJcTuoE+XX0HKmC93BV3uUd7Tz7pkx8FTKo6wlXCScKONuZwfM86nK0BaCnAweYIB/emy6sojw1uKlPtIG3C1gZPoK5tzutllun9IxyXWFxpLYHfR3BhTZP+hpK63/Rd/ElIxFuOEPY5JeAwlXuBj2HjTBZ7imHc5z7YU8SEMniOPpyo+OT84HsfGs3u+P3F2PBaQloFaXnMHKkpSrI9Mkfv4VNjX87qSX2MOBM5Ky24xzWkbhOfq8+Hn6FQ61ug3BuU87Ee4ETo2O+ZzkpzyUnxSR19juDUGvN6fkhUWVOCYzKe+lKKgkBI3CT64yfIedRe1a5d1BqeauBGkF5CEmNKRt9O3z7YAOeuc45Zqy6l4YuCReWGZiYfcS1yVgqShEdRBA68X0gepFegq5vHIEaKOiVpU6o+uFJA9N/WkdgviLkVR5KEtTWwONKc8Dg8UHry3TzG3Qgl7GKrF6aYwfDIEtbS3cnKmkFCT7Ek/rW6jFYcWlttS1qCUpGVKUcADxoa1y5TEKK7KluoZYZQVuOLOAlI5kmoE7eZGoF/GlC2IAP9kZVspY/4ix59B0Hmdo1edRL7QdT/AAEJShpu2OBTyhymOZ+XP5cjIHv4YlAHGpLbaRkkJSAPGs87nTr8XHftUm0wx3cNTpG7qs+w2/608mtMVkR4zbKeSEgZrcDVnUc+V2kcqClcD4aPwtlsAs5GyVJ3T7ZrVFutsSw0gSY7BSnh7lTiQUEbcOPLGPanGmOU8bXNdK3u7iPpLqSo7IWPqHvsrHjxVuVk7olR1qSlD7SlK5ALGTW6mliK5PiOqmNBnvd2hwAON+Cieis746UugPLeioU8AHk/I6E8gsbH26jyxWkKKKKKBkvbjk1abPDWUuPI4pLqT/dM+HqrcDyyelO0dluOylplIS2gAJA6VqgwmobRDfEpbh4nHFnKnFeJP+/ClI9/tXO1pmo9q9/uoTadzzWQPIU8ypbcVGV5Us/ShIypXoKi13VMkvIXJb7sLIQy0SOI5PIDrWa38c2qZ7Q9QyIqIlpgSVNr4RIkutqIUtSt8Z+59xUet+pNUSXmmLfLlOvjkltPEtYHjtvirXkdkqLld1z7ghzDh/uEO4SABgb8/DljlUn072a2XTU9MiC046HE8K1OuElPp5f9q3Mw5bvdVdpvS0+YzP1FqSI4+HcJcZXlKghRAKiBvtjZNXXpfTFlsNtQ1aozAbcAUpwDPeZ3Byc9KdzCjfDLjBlAZcSUrRjYgjBz7UhWpAt5t1rK1qUgtIdTulrOQSVcjjfbntT2za8Wu1x3bPgpKPiXTJC0HCkEn5CD0ITwj2xWy1XBz4ly23IBE9ocQUBhMlvo4j9lDmk+RSS6ISEIShA4UgAAeAFJbjbIlyQ2mW2olpfG2424ptaFeKVJII+9axgqUtKEqUtSUpTuSTgAVQfav2huahk/w1pl0qhrX3b77Z/9UrOOFP5P39OdyytM26dGcjXFUyYw4goU2/LcUnBGOWcZ8zvVRNdnsbSOtTKdkodt6UlcTiJ4kKVtwr8wDt48+hqZiybcPulrM1YrOxCbwXB8zygPqWRuf99KlWno3f3DjI+Vn5vfp/vypqSf3qX6ejdxb0uEYU8eL26VxndernfHjh0NJYCytyYT0kED2SkUqJxTLa7ij4VQZQp+Q9IfWhpHPh71QSpRP0jAG59s10eU7SJDUZpTr6wlCeZ8/AeJ8hSFqKuc83InN8KGlcTEdXNJ/Gr83PA6Z8eShqJxuIfl8Ljyd0gfS2fyjx8zv6UrxjlWpEAz1pDvGuox/dSxgjwdSP8AVI/+nmaXVonMKkRlobIDgwpsnkFg5SfvWhvopiY1hp5w927eILEhPyuMOvpQttY2KVAnYg7U8svtSGw5HdacbPJSFgg+4oGBSNTIioZYVGK0gDvXvmJHjtj9qxDtl2Ept+6zmccXJpatyeXPb2qRJ+lPpTVdHlMvokLQXEtAiKwk/M88oH7YTn7qPSuWOnkVXB34SMt1tol3Gx4Sr74qKXFlxbduunxCXnC8XONCwQPlISBj1p3iNTmimXdpK1vuq4W4zWwz+EDywT49SaTDTEp596Q5KYjKecLim2mioZPicjJHLOKuWrxsntJIryJEdDqD8qxmvMqS1GRxOrCQThKealHwA6mmM6fuDIPwlzSknmFNqA/RVK7TYvhJAmTXzJlgEJVuEtg8+EHqfE/pSS/rNz8pSIS5i+O45LfNEYH5E/4vxH9PLrTgAAMAYAoorcjAoopFdmUvMNB0cbIeT3rZ5LSflwfIcQPtVHhUl2eeC3r4GeSpWAQfJsHmfzHb16aLrY2JtodhNISle621KOTx+JJ3OeR8ia3MTlpuBhyUITxglhaf5sc0keON/MZ8KcTy2obirbXJS06iHLStsBfAkq3OEnCkH8ycEb8/HnVlRX2H2O8YUC2AB4YqB3tlMu+3SIw2t99xxJS02N0kIT835d8bnHKpLZLG+zBbRdnUOq2UthvPd8WN8nmv328q5Tj27c7LIXKdduTRRAcLbKhgysZ/+MHmfzHb16brXbolriJjQmQ22nzyT5kncnzNK/aiukjiKKKKoKZtX6hjaX09Lu0rcMpw2jqtw7JT9z9s081zh26auF8vwtEJwKg21RSojkt/ko+g5fegrefKdnTZEySrifkOqdcUBzUokn9TWpDzrYw24tI54SoivFFB26PpHpTPDYXcLqbo/wD3LQLUNHT8znqeXoB7ucllT8VbKHC2paMBY6UluRKIjEVklBkrEdKh/InBJPrwpVjzxXONCB/a5Cp6iSgAtx/Dhzur/MRz8APPLjWG0JbQlDaQlKRgADAAFZrcZFFFFUFFaZslEOI9JdCihpBWoJG5A8KorVnbjcXXXI2nISIjaSUl+QONw9NhnA/WguTUmp7PpmJ8TepqI6T9CMFS1/4Ujc1Tuqu3KRJQuPp62tstE4L0z5ln0SDgdOZNVLc7lMu0xyZcpLsmSs/M46riJ/6DypIOtB1vcZ8STZk3VyQmPHUwiUh9Wwb2Ckq9uv2pz01fIuo7JFukJQU0+ncD+VQ2UPYg1WHaTFNu7H4UPjKu7aioJzz2Bpn/APD1f3o8+fY1grjuoElH5FDAV9wR9qC47Q7EakTQgIS+9JWtxQTjjPGptOT1/uyPangchUStBTKXLWU7OEAjPipaz+qzXrR2rGr7Pu1qDb3fWt0Nl5zH9anfB2PPY+vPbOKCV0UUUBRRXhRJdCR0GaCIdquq/wCFNLPPR1D4+V/UxR4KPNXsN/XFcplRJJJyTzJqc9sGpXdQaylNkLRGt5VFZQT+EniVjxJ/QCoMdjQYooooP//Z",
  gender:"男",
  phone: "12345678",
  email: "123@qq.com",
  planetCode: "123",
  createTime: new Date(),
};
const router = useRouter();
onMounted(async () =>{
  const userListData = await myAxios.get('/user/search/tags', {
    paramsSerializer: params => {
      return qs.stringify(params, { indices: false })
    }
  })
      .then(function (response) {
        console.log('/user/search/tags succeed',response);
        Toast.success("请求成功");
        return response.data?.data;
      })
      .catch(function (error) {
        console.error('/user/search/tags error',error);
        Toast.fail("请求失败");
      });
  console.log(userListData)
  if(userListData){
    userListData.forEach(user =>{
      if(user.tags){
        user.tags = JSON.parse(user.tags);
      }
    })
    userList.value = userListData;
  }
})
const toEdit = (editKey: String,editName: String,currentValue: String) =>{
  router.push({
    path: '/user/edit',
    params:{
      a : 1
    },
    query: {
      editKey,
      editName,
      currentValue,
    }
  })

}
</script>

<style scoped>

</style>