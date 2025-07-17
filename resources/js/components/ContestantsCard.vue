<script setup>
import { Link, router } from '@inertiajs/vue3'
import moment from 'moment-timezone'
import { Dialog, DialogContent, DialogTrigger, DialogClose } from '@/components/ui/dialog'
import VoteTransactions from '@/components/VoteTransactions.vue'

defineProps(['contestant', 'isVoting', 'competitionSlug', 'transactions'])

function deleteContestant(slug) {
    // Return confirm dialog to proceed
    if (confirm('Are you sure you want to delete this contestant?')) {
        router.delete(route('contestant.destroy', slug), {
            //preserveScroll: true,
            onSuccess: () => {
              // Optional: show a success toast or redirect
              alert('Deleted')
            },
        })
    }
}
</script>

<template>
    <div class="contcards flex flex-col justify-between w-full max-w-xs mx-auto items-center border bdr rounded-lg mb-5 sm:mb-0 last:mb-0 p-3 py-5 shadow-sm">
      
      <div class="w-fit h-fit">
        <img
          :src="contestant.picture_path ? `/storage/${contestant.picture_path}` : '/assets/default_contestant.png'"
          class="w-28 h-28 object-cover object-center border-2 border-blue-500 rounded-full mb-3" />
      </div>
      
      <h1 class="text-base sm:text-lg font-bold text-[var(--echo-dark-400)] text-center">{{ contestant.name }}</h1>
      <p class="text-xs text-neutral-600/90 text-center mb-3">{{ contestant.title_of_piece }}</p>

      <div v-if="contestant.votes > 0" class="text-red-500 text-xs font-semibold mb-1.5">{{ contestant.votes.toLocaleString() }} Votes</div>
      
      <Link
        :href="isVoting ? `/competitions/${competitionSlug}/contestants/${contestant.slug}` : ''"
        class="btns-sm btn-grad"
        :class="{'opacity-60 pointer-events-none': !isVoting}">Vote</Link>

        <div v-if="$page.props.auth.user && $page.url.includes('contestants')" class="flex items-center font-semibold text-xs underline underline-offset-4 mt-2 space-x-3.5">
          <button
          @click="deleteContestant(contestant.slug)"
          class="">Remove Contestant</button>


          
          <Dialog>
              <DialogTrigger as-child>
                  <button class="text-neutral-500">Info</button>
              </DialogTrigger>
              <DialogContent class="bg-white">
                  <h1 class="text-base xsm:text-xl text-neutral-700 font-bold mb-2">{{ contestant.name }} Info</h1>

                  <div class="grid xsm:grid-cols-2 gap-x-3 gap-y-1">
                    <div class="contestant-info">
                        <h1>Time Registered</h1>
                        <p>{{ moment(contestant.created_at).format('D MMMM, YYYY') }}</p>
                    </div>

                    <div class="contestant-info">
                        <h1>Email</h1>
                        <p>{{ contestant.email }}</p>
                    </div>

                    <div class="contestant-info">
                        <h1>Phone</h1>
                        <p>{{ contestant.phone }}</p>
                    </div>

                    <div class="contestant-info">
                        <h1>Piece Title</h1>
                        <p>{{ contestant.title_of_piece }}</p>
                    </div>

                    <div class="contestant-info">
                        <h1>How long have they been writing?</h1>
                        <p>{{ contestant.writing_experience }}</p>
                    </div>

                    <div class="contestant-info">
                        <h1>How did they discovered their writing talent?</h1>
                        <p class="whitespace-pre-line">{{ contestant.discovery_story }}</p>
                    </div>

                    <div class="contestant-info">
                        <h1>Instagram Handle</h1>
                        <p class="whitespace-pre-line">{{ contestant.insta }}</p>
                    </div>
                  </div>


                  <VoteTransactions :transactions="transactions" :name="contestant.name" />
              </DialogContent>
          </Dialog>
        </div>
    </div>

</template>

<style scoped>
@reference "@/css/app.css";

.contcards {
  transition: all 0.3s ease;
}

.contcards:hover {
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
}
</style>