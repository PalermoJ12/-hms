<template>
    <div class="flex flex-col">
        <div class="text-2xl p-6 text-black va-title">PATIENTS</div>
        <div class="flex justify-start w-full">
            <VaCard class="w-[1150px] mx-20 min-h-[500px] mt-5">
                <VaCardTitle class="flex flex-row w-100 justify-between ">
                    <div>PATIENTS</div>
                    <div v-if="userAccess === 'admin'">
                        <button>
                            <VaIcon name="add_circle" size="large" class="hover:text-green-500"
                                @click="handleModalAdd" />
                        </button>
                    </div>

                </VaCardTitle>
                <VaCardContent>
                    <div class="relative overflow-x-auto">
                        <table class="w-full text-sm text-left rtl:text-right text-gray-500 dark:text-gray-400">
                            <!-- Table header -->
                            <thead
                                class="text-xs text-gray-700 uppercase bg-gray-50 dark:bg-gray-700 dark:text-gray-400">
                                <tr>
                                    <th scope="col" class="px-6 py-3">Name</th>
                                    <th scope="col" class="px-6 py-3">Gender</th>
                                    <th scope="col" class="px-6 py-3">Date of Birth</th>
                                    <th scope="col" class="px-6 py-3">Phone</th>
                                    <th scope="col" class="px-6 py-3">Action</th>
                                </tr>
                            </thead>
                            <!-- Table body -->
                            <tbody>
                                <tr v-for="(record, index) in paginatedRecords" :key="index"
                                    class="bg-white border-b dark:bg-gray-800 dark:border-gray-700">
                                    <td class="px-6 py-4">{{ record.user.name }}</td>
                                    <td class="px-6 py-4">{{ record.gender }}</td>
                                    <td class="px-6 py-4">{{ record.date_of_birth }}</td>
                                    <td class="px-6 py-4">{{ record.phone }}</td>
                                    <td class="px-6 py-4 space-x-4">
                                        <button @click="handleShowViewModal(record)">
                                            <VaIcon name="visibility" size="large" class="hover:text-blue-700" />
                                        </button>
                                        <button @click="handleShowUpdateModal(record)">
                                            <VaIcon name="edit" size="large" class="hover:text-blue-700" />
                                        </button>
                                        <button v-if="userAccess === 'admin'" @click="handleShowDeleteModal(record)">
                                            <VaIcon name="delete" size="large" class="hover:text-blue-700" />
                                        </button>

                                    </td>
                                </tr>
                            </tbody>
                        </table>
                    </div>

                    <div class="flex justify-end mt-4 mr-4">
                        <VaPagination v-model="currentPage" :pages="totalPages" />
                    </div>
                </VaCardContent>
            </VaCard>
        </div>


    </div>

    <VaModal ref="modal" v-model="userInfoModal" ok-text="Next" @ok="handleNextSubmit">
        <div class="va-h5 flex flex-row justify-between">
            <span> Patient Information</span>
            <h6 class="text-sm text-red-600">
                {{ error }}
            </h6>
        </div>

        <hr>
        <div class="mt-4">
            <VaForm ref="myForm" stateful class="mb-2 flex flex-col gap-4 mt-8" preset="danger">

                <VaInput v-model="newRecord.user.name" name="Name" label="Name" />
                <VaSelect v-model="newRecord.gender" :options="options.map(item => item.gender)" label="Gender" />
                <VaInput v-model="newRecord.date_of_birth" type="date" name="Date of Birth" label="Date of Birth" />
                <VaInput type="number" v-model="newRecord.phone" name="Phone" label="Phone" />
                <VaTextarea v-model="newRecord.address" name="Address" label="Address" />

            </VaForm>
        </div>

    </VaModal>



    <VaModal ref="modal" v-model="userCredentialModal" ok-text="Submit" @ok="handleCreateUser">
        <div class="va-h5 flex flex-row justify-between">
            <span> Patient Credential</span>

        </div>

        <hr>
        <div class="mt-4">
            <VaForm ref="myForm" stateful class="mb-2 flex flex-col gap-4 mt-8" preset="danger">
                <VaInput v-model="newRecord.user.email" type="email" name="Email" label="Email" />
                <VaInput type="password" v-model="newRecord.user.password" name="Password" label="Password" />
                <VaInput type="password" v-model="newRecord.user.password_confirmation" name="Confirm Password"
                    label="Confirm Password" />

            </VaForm>
        </div>

    </VaModal>


    <VaModal ref="modal" v-model="viewUserModal" ok-text="Ok">


        <div class="mt-4">

            <VaCardContent>
                <div class="flex flex-col w-100">
                    <div>
                        <h1 class="va-h6 text-2xl"> {{ selectedRecord.user.name }}</h1>

                    </div>
                    <hr>
                    <div>
                        <div><span class="font-light">Gender</span> : {{ selectedRecord.gender.toUpperCase() }}</div>
                        <div><span class="font-light">Birthdate</span> : {{ selectedRecord.date_of_birth }}</div>
                        <div><span class="font-light">Contact</span> : {{ selectedRecord.phone }}</div>
                        <div><span class="font-light">Address</span> : {{ selectedRecord.address }}</div>
                        <div><span class="font-light">Email</span> : {{ selectedRecord.user.email }}</div>
                    </div>
                </div>
            </VaCardContent>

        </div>

    </VaModal>


    <VaModal ref="modal" v-model="userUpdateModal" ok-text="Update" @ok="handleUpdate">
        <div class="va-h5 flex flex-row justify-between">
            <span> Patient Information</span>

        </div>

        <hr>
        <div class="mt-4">
            <VaForm ref="myForm" stateful class="mb-2 flex flex-col gap-4 mt-8" preset="danger">

                <VaInput v-model="selectedRecord.user.name" name="Name" label="Name" />
                <VaSelect v-model="selectedRecord.gender" :options="options.map(item => item.gender)" label="Gender" />
                <VaInput v-model="selectedRecord.date_of_birth" type="date" name="Date of Birth"
                    label="Date of Birth" />
                <VaInput v-model="selectedRecord.phone" name="Phone" label="Phone" />
                <VaTextarea v-model="selectedRecord.address" name="Address" label="Address" />

            </VaForm>
        </div>

    </VaModal>
    <VaModal ref="modal" v-model="deleteModal" ok-text="Confirm" @ok="handleDelete">
        <div class="va-h5 flex flex-row justify-between">
            <h6> Confirmation</h6>

        </div>

        <hr>
        <div class="mt-4">
            <div class="flex flex-col w-100">Are you sure you want to delete this patient?</div>
        </div>

    </VaModal>

</template>

<script>
import axiosClient from "../axios-config/axios-client";
import { useToast } from 'vuestic-ui'
const { notify } = useToast();
export default {


    beforeMount() {
        this.$store.dispatch("getPatientRecords");
        this.$store.dispatch("getDoctors");
    },
    data() {
        return {
            userUpdateModal: false,
            userInfoModal: false,
            userCredentialModal: false,
            deleteModal: false,
            viewUserModal: false,
            selectedRecord: [],
            currentPage: 1,
            perPage: 5,
            currentPageView: 1,
            perPageView: 3,
            updateData: {},
            userAccess: this.$store.state.user_access,
            newRecord: {
                user: {},
                gender: "",
                date_of_birth: "",
                phone: "",
                address: ""
            },
            options: [
                { id: 1, gender: "male" },
                { id: 2, gender: "female" }
            ],
            error: "",
            errorResponse: {}
        };
    },
    computed: {
        records() {
            return this.$store.state.patientRecords;
        },
        doctors() {
            console.log(this.$store.state.doctors)
            return this.$store.state.doctors;

        },
        totalPages() {
            return Math.ceil(this.records.length / this.perPage);
        },
        paginatedRecords() {
            const startIndex = (this.currentPage - 1) * this.perPage;
            return this.records.slice(startIndex, startIndex + this.perPage);
        },
        totalPagesView() {
            return Math.ceil(this.selectedRecord.length / this.perPageView);
        },
        paginatedRecordsView() {

            const startIndex = (this.currentPageView - 1) * this.perPageView;
            return this.selectedRecord.slice(startIndex, startIndex + this.perPageView);
        }
    },
    methods: {

        handleModalAdd() {
            this.userInfoModal = !this.userInfoModal;

        },
        handleShowViewModal(record) {
            this.selectedRecord = record;
            this.viewUserModal = !this.viewUserModal;
        },
        handleShowUpdateModal(record) {
            this.selectedRecord = record;
            this.userUpdateModal = !this.userUpdateModal;
        },
        handleShowDeleteModal(record) {
            this.selectedRecord = record;
            this.deleteModal = !this.deleteModal;
        },
        async handleUpdateSubmit() {

            await axiosClient.put(`/medical-records/${this.updateData.id}`, this.updateData)
                .then(() => {

                    this.$store.dispatch("getPatientRecords");
                    notify('Successfully updated');
                })
                .catch((err) => console.log(err));
        },
        handleNextSubmit() {
            if ((this.newRecord.user.name != "" && this.newRecord.gender != "" && this.newRecord.date_of_birth != "" && this.newRecord.phone != "" && this.newRecord.address != "")) {
                this.userInfoModal = false;
                this.error = "";
                setTimeout(() => {
                    this.userCredentialModal = !this.userCredentialModal;
                }, 1000)
            } else {
                this.userInfoModal = true;
                this.error = "Please fill all fields";
            }
        },
        async handleCreateUser() {
            await axiosClient.post("/patients", this.newRecord)
                .then(() => {
                    this.userCredentialModal = false;
                    this.$store.dispatch("getPatientRecords");
                    this.errorResponse = {};
                    notify('Successfully created');
                })
                .catch((err) => {
                    this.userCredentialModal = true;
                    this.errorResponse = err.response.data.message;
                    console.log("=====>", this.errorResponse = err.response.data.message);

                });
        },
        async handleUpdate() {
            await axiosClient.put(`/patients/${this.selectedRecord.id}`, this.selectedRecord)
                .then(() => {
                    this.userUpdateModal = false;
                    this.$store.dispatch("getPatientRecords");
                    notify('Successfully updated');
                })
                .catch((err) => {
                    this.userUpdateModal = true;
                    console.log(err)
                });
        },
        async handleDelete() {
            await axiosClient.delete(`/patients/${this.selectedRecord.id}`, this.selectedRecord)
                .then(() => {
                    this.deleteModal = false;
                    this.$store.dispatch("getPatientRecords");
                    notify('Successfully deleted.')
                })
                .catch((err) => {
                    this.deleteModal = true;
                    console.log(err)
                });
        },

    }
};
</script>

<style scoped>
/* Add your styles here */
</style>