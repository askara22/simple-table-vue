<template>
	<div>
	  <b-row class="text-center">
		<b-col>
		  <div
			class="filter-card p-3"
			:class="{
			  'bg-active-filter': totalIsActive,
			  'bg-light': !totalIsActive,
			}"
			@click="totalCustomersIsActiveFilter"
		  >
			<h6>Total Customers</h6>
			<h4>
			  <strong>{{ totalCustomers }}</strong>
			</h4>
		  </div>
		</b-col>
		<b-col>
		  <div
			class="filter-card p-3"
			:class="{
			  'bg-active-filter': lunasIsActive,
			  'bg-light': !lunasIsActive,
			}"
			@click="lunasCustomersIsActiveFilter"
		  >
			<h6 class="text-secondary">Customers Lunas</h6>
			<h4>
			  <strong>{{ lunasCustomers }}</strong>
			</h4>
		  </div>
		</b-col>
		<b-col>
			<div
			  class="filter-card p-3"
			  :class="{
				'bg-active-filter': konfirmasiIsActive,
				'bg-light': !konfirmasiIsActive,
			  }"
			  @click="konfirmasiCustomersIsActiveFilter"
			>
			  <h6 class="text-secondary">Customers Konfirmasi</h6>
			  <h4>
				<strong>{{ konfirmasiCustomers }}</strong>
			  </h4>
			</div>
		  </b-col>
		  <b-col>
			<div
			  class="filter-card p-3"
			  :class="{
				'bg-active-filter': prosesIsActive,
				'bg-light': !prosesIsActive,
			  }"
			  @click="prosesCustomersIsActiveFilter"
			>
			  <h6 class="text-secondary">Customers Proses</h6>
			  <h4>
				<strong>{{ prosesCustomers }}</strong>
			  </h4>
			</div>
		  </b-col>
	  </b-row>
	</div>
  </template>
  
  <script>
  export default {
	name: "CustomerOverview",
	props: {
	  totalCustomers: Number,
	  lunasCustomers: Number,
	  konfirmasiCustomers: Number,
	  prosesCustomers: Number,
	},
	data() {
	  return {
		totalIsActive: true,
		lunasIsActive: false,
		konfirmasiIsActive: false,
		prosesIsActive: false,
	  };
	},
	methods: {
	  totalCustomersIsActiveFilter() {
		this.totalIsActive = true;
		this.lunasIsActive = false;
		this.konfirmasiIsActive = false;
		this.prosesIsActive= false;
		this.$emit("totalCustomersIsActive");
	  },
	  lunasCustomersIsActiveFilter() {
		this.totalIsActive = false;
		this.lunasIsActive = true;
		this.konfirmasiIsActive = false;
		this.prosesIsActive= false;
		this.$emit("lunasCustomerIsActive");
	  },
	  konfirmasiCustomersIsActiveFilter() {
		this.totalIsActive = false;
		this.lunasIsActive = false;
		this.konfirmasiIsActive = true;
		this.prosesIsActive= false;
		this.$emit("konfirmasiCustomerIsActive");
	  },
	  prosesCustomersIsActiveFilter() {
		this.totalIsActive = false;
		this.activeIsActive = false;
		this.konfirmasiIsActive = false;
		this.prosesIsActive= true;
		this.$emit("prosesCustomerIsActive");
	  },
	},
  };
  </script>
  
  <style>
  .filter-card:hover {
	cursor: pointer;
	text-decoration: underline;
  }
  
  .bg-active-filter {
	background-color: #e9f1fe;
	color: #074297;
  }
  </style>