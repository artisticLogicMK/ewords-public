<script setup>
import { nextTick, onMounted, ref } from 'vue'
import { Head, Link, useForm, router } from '@inertiajs/vue3'
import DashboardLayout from '@/layouts/DashboardLayout.vue'
import { Input } from '@/components/ui/input'
import { Label } from '@/components/ui/label'
import { PhFloppyDisk, PhLink, PhToggleRight, PhToggleLeft } from '@phosphor-icons/vue'
import InputError from '@/components/InputError.vue';
import Editor from '@/components/Editor.vue'

const { competition } = defineProps(['competition'])

const form = useForm({
  title: competition.title,
  active: competition.active,
  voting_active: competition.voting_active,
  registration_active: competition.registration_active,
  set_winners: competition.set_winners,
  description: competition.description,
  content: competition.content,
  cover: competition.cover,
  //stage: competition.stage,
  past_winners_content: competition.past_winners_content,
  winner: competition.winner,
  winner_pic: competition.winner_pic,
  first_runner: competition.first_runner,
  first_runner_pic: competition.first_runner_pic,
  second_runner: competition.second_runner,
  second_runner_pic: competition.second_runner_pic,
  registration_closes: competition.registration_closes,
  first_voting_starts: competition.first_voting_starts,
  first_voting_ends: competition.first_voting_ends,
  second_voting_starts: competition.second_voting_starts,
  second_voting_ends: competition.second_voting_ends
})


// file input handler
function handleFileChange(e, field) {
  form[field] = e.target.files[0]
}


function submit() {
    form.post(route('competition.update', competition.slug), {
        method: 'put',
        preserveScroll: true,
        forceFormData: true,
        onSuccess: async () => {
            await nextTick()
            if (document.getElementById("flash")) document.getElementById("flash").scrollIntoView()
        },
    })
}


const secondForm = useForm({
    limit: 30,
    criteria: null
})

function secondStage() {
    // Return confirm dialog to proceed
    if (!confirm("Are you sure you want to take this competition to the second stage? This cant't be reversed!")) return

    // Verify one more time with input dialog
    if (prompt("Type 'yes' to continue.") !== 'yes') return
        
    secondForm.post(route('competition.secondStage', competition.slug), {
        method: 'put',
        preserveScroll: true,
        onSuccess: async () => {
            await nextTick()
            if (document.getElementById("flash")) document.getElementById("flash").scrollIntoView()
        },
    })
}



function deleteCompetition(slug) {
    // Return confirm dialog to proceed
    if (confirm('Are you sure you want to delete this competition?')) {

        // Verify one more time with input dialog asking for title
        if (prompt("Type title of competition.") !== competition.title) return

        router.delete(route('competition.destroy', slug), {
            //preserveScroll: true,
            onSuccess: () => {
                // Optional: show a success toast or redirect
                alert('Deleted')
            },
        })
    }
}



// Cleanup files
function cleanupFiles(slug) {
    if (form.active === 1 || form.registration_active === 1 || form.voting_active === 1) {
        alert("The competition, and its registration and voting must be off first!")
        return
    }

    // Return confirm dialog to proceed
    if (confirm('Are you sure to delete  all contestants videos of this competition?')) {

        // Verify one more time with input dialog asking for title
        if (prompt("Type title of competition.") !== competition.title) return

        router.delete(route('competition.cleanup-files', slug), {
            //preserveScroll: true,
            onSuccess: async () => {
                await nextTick()
                if (document.getElementById("flash")) document.getElementById("flash").scrollIntoView()
            },
        })
    }
}


const toggles = [
    { name:'Competition is Ongoing', key:'active'},
    { name:'Registration Active', key:'registration_active'},
    { name:'Voting Active', key:'voting_active'},
]


let competitionLink = ref('')

onMounted(() => {
    competitionLink.value = window.location.host + '/competitions/' + competition.slug
})
</script>

<template>
    <Head :title="`${competition.title}:Dashboard`" />

    <DashboardLayout>
        <div class="flex justify-end px-4 sm:px-6 py-2 border-b bdr" style="display:none">
            <Link :href="`/competitions/${competition.slug}/contestants`" class="text-blue-600 text-sm border border-blue-600 px-3 py-1 rounded-md hover:text-white hover:bg-blue-600">View Competition Contestants.</Link>
        </div>


        <div class="relative px-4 sm:px-6 py-5 pb-8">

            <div v-if="$page.props.flash.error" id="flash" class="alerts error-alert mb-5">{{ $page.props.flash.error }}</div>

            <div v-if="$page.props.flash.success" id="flash" class="alerts success-alert mb-5">{{ $page.props.flash.success }}</div>

            <p class="mb-4 note">
                <span v-if="competition.stage === 'end'">This competition has ended</span>
                <span v-else>This competition is in <strong>{{ competition.stage }} Stage.</strong> <a v-if="competition.stage === '1st'" href="#second" class="text-blue-600">Set Second Stage.</a> <a v-if="false" href="#end" class="text-orange-600">End Competition.</a></span>
            </p>
            
            
            <form @submit.prevent="submit">
                <div
                    v-for="{ name, key } in toggles" :key="key" 
                    class="flex justify-center items-center space-x-3 mb-2 last:mb-8"
                >
                    <span class="text-black/80 text-base">{{ name }}:</span>
                    <button @click.prevent>
                        <PhToggleRight @click="form[key] = 0" v-if="form[key] === 1" weight="fill" class="text-blue-600" size="45" />
                        <PhToggleLeft @click="form[key] = 1" v-else weight="fill" class="text-neutral-300" size="45" />
                    </button>
                </div>

                <div class="mb-5">
                    <Label :required="true">Competition Title</Label>
                    <Input type="text" v-model="form.title" placeholder="Title of competition..."  />
                    <InputError :message="form.errors.title" />
                </div>

                <div class="mb-5">
                    <Label>Competition Cover Picture <template #description>Use a square size image</template></Label>
                    <img v-if="form.cover && typeof form.cover === 'string'" :src="`/storage/${form.cover}`" class="w-16 mb-3">
                    <Input type="file" @change="e => handleFileChange(e, 'cover')" />
                </div>

                <div class="mb-5">
                    <Label>
                        Competition Link
                        <template #description>Cant be modified. Select to copy.</template>
                    </Label>
                    <Input type="text" v-model="competitionLink" class="bg-neutral-200/70" />
                </div>

                <div class="mb-5">
                    <Label :required="true">Competition Description</Label>
                    <textarea v-model="form.description" class="input" rows="2" style="height:auto"></textarea>
                    <InputError :message="form.errors.description" />
                </div>

                <div class="dbox px-3 sm:px-4 py-4 mb-5">
                    <p class="mb-5 note">This section is only used to display real-time countdowns of the competition. Please set only 1 of these at a time, or else the layout of the site will break.</p>

                    <div class="mb-4">
                        <Label>Registration Closes At</Label>
                        <Input type="datetime-local" v-model="form.registration_closes"  />
                    </div>

                    <div class="mb-4">
                        <Label>Voting Starts At</Label>
                        <Input type="datetime-local" v-model="form.first_voting_starts" />
                    </div>

                    <div class="mb-4">
                        <Label>First Stage Voting Ends At</Label>
                        <Input type="datetime-local" v-model="form.first_voting_ends" />
                    </div>

                    <div class="mb-4">
                        <Label>Second Stage Voting Starts At</Label>
                        <Input type="datetime-local" v-model="form.second_voting_starts" />
                    </div>

                    <div>
                        <Label>Second Stage Voting Ends At</Label>
                        <Input type="datetime-local" v-model="form.second_voting_ends" />
                    </div>
                </div>


                <div class="mb-5">
                    <Label>Competition Content</Label>
                    <Editor :content="form.content" v-model="form.content" />
                </div> 


                <div class="dbox px-3 sm:px-4 py-4 mb-5">
                    <h1 class="flex items-center text-neutral-600 text-lg font-semibold mb-3">
                        <input type="checkbox" v-model="form.set_winners" :true-value="1" :false-value="0" class="w-4.5 h-4.5 accent-blue-500 cursor-pointer mr-2"> Set Winners
                    </h1>
                    <div :class="{'opacity-50 pointer-events-none': form.set_winners === 0}">
                        <div class="mb-5">
                            <Label>Winner</Label>
                            <Input type="text" v-model="form.winner" placeholder="Name" class="mb-3" />
                            <img v-if="form.winner_pic && typeof form.winner_pic === 'string'" :src="`/storage/${form.winner_pic}`" class="w-16 mb-2">
                            <Input type="file" @change="e => handleFileChange(e, 'winner_pic')" placeholder="30" />
                        </div>

                        <div class="mb-5">
                            <Label>1st Runner Up</Label>
                            <Input type="text" v-model="form.first_runner" placeholder="Name" class="mb-3" />
                            <img v-if="form.first_runner_pic && typeof form.first_runner_pic === 'string'" :src="`/storage/${form.first_runner_pic}`" class="w-16 mb-2">
                            <Input type="file" @change="e => handleFileChange(e, 'first_runner_pic')" placeholder="30" />
                        </div>

                        <div>
                            <Label>2nd Runner Up</Label>
                            <Input type="text" v-model="form.second_runner" placeholder="Name" class="mb-3" />
                            <img v-if="form.second_runner_pic && typeof form.second_runner_pic === 'string'" :src="`/storage/${form.second_runner_pic}`" class="w-16 mb-2">
                            <Input type="file" @change="e => handleFileChange(e, 'second_runner_pic')" placeholder="30" />
                        </div>
                    </div>
                </div>


                <div class="mb-5">
                    <Label>Content to show in 'Past Winners' Page</Label>
                    <Editor :content="form.past_winners_content" v-model="form.past_winners_content" />
                </div>


                <button class="fixed bottom-3 right-1/2 w-12 h-12 flex items-center justify-center bg-blue-500 text-white text-2xl rounded-full shadow-md">
                    <PhFloppyDisk weight="fill" />
                </button>
                
            </form>



            <div v-if="competition.stage === '1st'" id="second" class="dbox px-3 sm:px-4 py-4 mb-8">
                <form @submit.prevent="secondStage">
                    <h1 class="text-neutral-600 text-xl font-semibold mb-3">Determine Second Stage</h1>
                    <div class="mb-3">
                        <Label>Number of Participant to Carry</Label>
                        <Input type="number" v-model="secondForm.limit" placeholder="30" required />
                    </div>

                    <div class="mb-3">
                        <Label>Minimum of Votes</Label>
                        <Input type="number" v-model="secondForm.criteria" placeholder="00" required />
                    </div>

                    <p class="mb-3 note">Top <strong>{{ secondForm.limit }}</strong> of the participants will be taken that meets the required minimum of <strong>{{ secondForm.criteria || 0 }}</strong> votes, ordered by number of votes, and then time registered, and a new vote will commence afresh. The rest of the participants will be deleted. <span class="text-orange-600">This can't be reversed!</span></p>
                    <button class="btns btn-grad">Start Second Stage!</button>
                </form>
            </div>



            <div class="mb-6">
                <p class="note mb-3">If you're sure this competition is over, delete all video files of contestants with this button to save up hosting storage space.</p>
                <button @click="cleanupFiles(competition.slug)" class="btns btns-sm bg-orange-600 block">! Remove Contestants Videos</button>
            </div>

            <button @click="deleteCompetition(competition.slug)" class="text-red-500 block mb-8">Delete this Competition</button>

        </div>
    </DashboardLayout>
</template>
