<template>
	<div>
	  <b-row>
		<b-alert v-model="showSuccessAlert" variant="success" dismissible>
		  {{ alertMessage }}
		</b-alert>
	  </b-row>
	  <b-row>
		<customer-overview
		  :totalCustomers="numberOfCustomers"
		  :lunasCustomers="lunasCustomers"
		  :konfirmasiCustomers="konfirmasiCustomers"
		  :prosesCustomers="prosesCustomers"
		  @totalCustomersIsActive="setFilterTotalIsActive"
		  @lunasCustomerIsActive="setFilterLunasIsActive"
		  @konfirmasiCustomerIsActive="setFilterKonfirmasiIsActive"
		  @prosesCustomerIsActive="setFilterProsesIsActive"
		></customer-overview>
	  </b-row>
	  <b-row class="mt-3">
		<b-card>
		  <b-row align-h="between">
			<b-col cols="9">
			  <h3>{{ tableHeader }}</h3>
			</b-col>
			<b-col cols="1">
				<b-row>
				  <b-col>
					<b-button
					  variant="primary"
					  id="show-btn"
					  @click="getCustomerData"
					>
					  <b-icon-arrow-clockwise class="text-white"></b-icon-arrow-clockwise>
					</b-button>
				  </b-col>
				</b-row>
			  </b-col>
			  <b-col cols="1">
				<b-row>
				  <b-col>
					<download-excel :data="items">		
						<b-button
						variant="primary"
						id="show-btn"
						>
						<b-icon-download class="text-white"></b-icon-download>
					</b-button>
				</download-excel>
				  </b-col>
				</b-row>
			  </b-col>
			  <b-col>
				<b-col cols="12" class="mb-3">
					<b-button variant="success" @click="bayarSelectedItems">Bayar</b-button>
				  </b-col>
			  </b-col>
		  </b-row>
		  <b-col cols="3">
			<b-form-group
			  label="Filter"
			  label-for="filter-input"
			  label-cols-sm="3"
			  label-align-sm="right"
			  label-size="sm"
			  class="mb-0"
			>
			  <b-input-group size="sm">
				<b-form-input
				  id="filter-input"
				  v-model="filter"
				  type="search"
				  placeholder="Type to Search"
				></b-form-input>
				<b-input-group-append>
				  <b-button :disabled="!filter" @click="filter = ''">Clear</b-button>
				</b-input-group-append>
			  </b-input-group>
			</b-form-group>
		  </b-col>
		  <b-row class="mt-3">
			<b-col>
			  <b-form-group label="Min Value (Rp)" label-for="min-total">
				<b-form-input id="min-total" v-model="minTotal" type="number" min="0" placeholder="e.g. 90000000" ></b-form-input>
			  </b-form-group>
			</b-col>
			<b-col>
			  <b-form-group label="Max Value (Rp)" label-for="max-total">
				<b-form-input id="max-total" v-model="maxTotal" type="number" min="0" placeholder="e.g. 95000000" ></b-form-input>
			  </b-form-group>
			</b-col>
			<b-col align-self="end">
			  <b-button variant="primary" @click="applyTotalFilter">Apply</b-button>
			</b-col>
		  </b-row>
		  <b-row class="mt-3">
			<b-table
			  striped
			  hover
			  :items="items"
			  :fields="fields"
			  :sort-by.sync="sortBy"
              :sort-desc.sync="sortDesc"
			  :current-page="currentPage"
      		  :per-page="perPage"
      		  :filter="filter"
			  label-sort-asc=""
			  label-sort-desc=""
			  label-sort-clear=""
			  class="text-center"
			>
			<template #head(checkbox)="data">
				<b-form-checkbox
				  v-model="selectAll"
				  @change="selectAllRows"
				></b-form-checkbox>
			  </template>
			  <template #cell(checkbox)="data">
				<b-form-checkbox
				  v-model="selectedRows"
				  :value="data.item.id"
				></b-form-checkbox>
			  </template>
			  <template #cell(id)="data">
				{{
				  `${data.item.id}`
				}}
			  </template>
			</b-table>
			<b-col sm="5" md="6" class="my-1">
				<b-form-group
				  label="Per page"
				  label-for="per-page-select"
				  label-cols-sm="6"
				  label-cols-md="4"
				  label-cols-lg="3"
				  label-align-sm="right"
				  label-size="sm"
				  class="mb-0"
				>
				  <b-form-select
					id="per-page-select"
					v-model="perPage"
					:options="pageOptions"
					size="sm"
				  ></b-form-select>
				</b-form-group>
			  </b-col>
			  <b-col sm="7" md="6" class="my-1">
				<b-pagination
				  v-model="currentPage"
				  :total-rows="totalRows"
				  :per-page="perPage"
				  align="fill"
				  size="sm"
				  class="my-0"
				></b-pagination>
			  </b-col>
		  </b-row>
		</b-card>
	  </b-row>
	</div>
  </template>
  
  <script>
  import axios from "axios";
  import CustomerOverview from "@/components/CustomerOverview.vue";
  
  export default {
	components: {
	  CustomerOverview,
	},
	data() {
	  return {
		fields: [
		  {
			key: 'checkbox',
			label: ''
		  },
		  {
			key: "no_kewajiban",
			label: "No. Kewajiban",
			sortable: true,
		  },
		  {
			key: "no_polisi",
			label: "No. Polisi",
			sortable: true,
		  },
		  {
			key: "pemilik",
			label: "Pemiliki",
			sortable: true,
		  },
		  {
			key: "peserta",
			label: "Peserta",
			sortable: true,
		  },
		  {
			key: "nomor_va",
			label: "Nomor VA",
			sortable: true,
		  },
		  {
			key: "harga_terbentuk",
			label: "Harga Terbentuk (Rp)",
			sortable: true,
		  },
		  {
			key: "biaya_admin",
			label: "Biaya Admin ex PPN (Rp)",
			sortable: true,
		  },
		  {
			key: "ppn",
			label: "PPN (Rp)",
			sortable: true,
		  },
		  {
			key: "total",
			label: "Total (Rp)",
			sortable: true,
		  },
		  {
			key: "tanggal_lelang",
			label: "Tanggal Lelang",
			sortable: true,
		  },
		  {
			key: "tanggal_tempo",
			label: "Tanggal Jatuh Tempo",
			sortable: true,
		  },
		  {
			key: "tanggal_lunas",
			label: "Tanggal Lunas",
			sortable: true,
		  },
		  {
			key: "status",
			label: "Status",
			sortable: true,
		  },
		],
		items: [],
		numberOfCustomers: 0,
		lunasCustomers: 0,
		konfirmasiCustomers: 0,
		prosesCustomers: 0,
		lunasCustomersData: [],
		konfirmasiCustomersData: [],
		prosesCustomersData: [],
		customerId: 0,
		tableHeader: "",
		showSuccessAlert: false,
		alertMessage: "",
		totalRows: 1,
        currentPage: 1,
        perPage: 5,
        pageOptions: [5, 10, 15, 20, 50, 100],
		filter: null,
        filterOn: [],
		selectedRows: [],
		selectAll: false,
		minTotal: null,
        maxTotal: null,
	  };
	},
	mounted() {
	  this.getCustomerData();
	  this.totalRows = this.items.length;
	},
	methods: {
	  selectAllRows() {
        if (this.selectAll) {
          this.selectedRows = this.items.map(item => item.id);
        } else {
          this.selectedRows = [];
        }
      },
	  bayarSelectedItems() {
   		 if (this.selectedRows.length === 0) {
      		alert('Pilih setidaknya satu item untuk membayar.');
     	 return;
    	}

   		 if (confirm('Anda yakin ingin membayar item terpilih?')) {

     		 const selectedIds = this.selectedRows;

			selectedIds.forEach(id => {
				const selectedItem = this.items.find(item => item.id === id);
				if (selectedItem) {
					selectedItem.status = 'LUNAS';
					axios.put(`http://localhost:3000/piutangunit/${id}`, { status: 'LUNAS' })
					.then(response => {
					console.log(`Item dengan id ${id} berhasil diperbarui menjadi LUNAS.`);
					})
					.catch(error => {
					console.error(`Gagal memperbarui status item dengan id ${id}: ${error.message}`);
					});
				}
			});
			this.selectedRows = [];
			}
		},
		applyTotalFilter() {
			if ((this.minTotal !== null && this.minTotal !== '') && (this.maxTotal !== null && this.maxTotal !== '')) {
				this.items = this.items.filter(item => {
				return item.total >= this.minTotal && item.total <= this.maxTotal;
				});
			} else if (this.minTotal !== null && this.minTotal !== '') {
				this.items = this.items.filter(item => item.total >= this.minTotal);
			} else if (this.maxTotal !== null && this.maxTotal !== '') {
				this.items = this.items.filter(item => item.total <= this.maxTotal);
			} else {
				
				this.getCustomerData();
			}
		},
	  getCustomerData() {
		axios
		  .get("http://localhost:3000/piutangunit/")
		  .then((response) => {
			this.tableHeader = "PIUTANG UNIT";
			this.items = response.data;
			this.numberOfCustomers = response.data.length;
			this.lunasCustomersData = response.data.filter(
            (item) => item.status === "LUNAS"
            );
			this.konfirmasiCustomersData = response.data.filter(
				(item) => item.status === "KONFIRMASI PEMBAYARAN"
			)
			this.prosesCustomersData = response.data.filter(
				(item) => item.status === "PROSES PEMBAYARAN"
			)
			this.lunasCustomers = this.lunasCustomersData.length;
			this.konfirmasiCustomers = this.konfirmasiCustomersData.length;
			this.prosesCustomers = this.prosesCustomersData.length;
		  })
		  .catch((error) => {
			console.log(error);
		  });
	  },
	  getRowData(id) {
		this.customerId = id;
	  },
	  setFilterTotalIsActive() {
		this.tableHeader = "Total Customers";
		this.getCustomerData();
	  },
	  setFilterLunasIsActive() {
		this.tableHeader = "Customers Lunas";
		this.items = this.lunasCustomersData;
	  },
	  setFilterKonfirmasiIsActive() {
		this.tableHeader = "Customers Konfirmasi";
		this.items = this.konfirmasiCustomersData;
	  },
	  setFilterProsesIsActive() {
		this.tableHeader = "Customers Proses";
		this.items = this.prosesCustomersData;
	  }
	},
  };
  </script>
  
  <style>
  .action-item:hover {
	cursor: pointer;
  }
  </style>