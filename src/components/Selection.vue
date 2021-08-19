<!-- Copyright (c) 2021 MintJams Inc. Licensed under MIT License. -->

<template>
	<div class="rounded text-small">
		<div class="d-flex flex-wrap pl-2 pt-2">
			<div v-for="id in selectables" :key="id" class="mr-2 mb-2 c-pointer" v-on:click.prevent.stop="onClick(id)">
				<Badge class="shadow-sm"
					:authorizable="ui.$getAuthorizable(objects.map[id])"
					:item="ui.$getItem(objects.map[id])"
					:icon="ui.$getIcon(objects.map[id])"
					:label="ui.$getLabel(objects.map[id])"
					:maxLabelWidth="maxLabelWidth"
					:class="{'bg-primary': isSelected(id)}">
					<span v-if="multiple" class="ml-2 text-small text-white-50 text-shadow"><i class="fas fa-plus"></i></span>
				</Badge>
			</div>
		</div>
	</div>
</template>

<script>
/* eslint-disable no-console */

export default {
	props: {
		'multiple': {
			'type': Boolean,
			'default': true
		},
		'maxLabelWidth': {
			'type': String,
			'default': '10rem'
		},
	},
	data() {
		return {
			'selected': {
				'index': [],
				'map': {}
			},
			'objects': {
				'index': [],
				'map': {}
			},
			'ui': null
		};
	},
	created() {
		let vm = this;
		class UI {
			constructor() {
				this.$getIdentifier = function(o) {
					const authorizable = this.$getAuthorizable(o);
					if (authorizable) {
						return authorizable.id;
					}

					const item = this.$getItem(o);
					if (item) {
						return item.path;
					}

					return o;
				};
				this.$getAuthorizable = function(/* o */) {
					return undefined;
				};
				this.$getItem = function(/* o */) {
					return undefined;
				};
				this.$getIcon = function(/* o */) {
					return undefined;
				};
				this.$getLabel = function(o) {
					const authorizable = this.$getAuthorizable(o);
					if (authorizable) {
						return undefined;
					}

					const item = this.$getItem(o);
					if (item) {
						return undefined;
					}

					return '' + o;
				};
				this.$comparator = function(a, b) {
					const _this = this;
					const _text = function(o) {
						const authorizable = _this.$getAuthorizable(o);
						if (authorizable) {
							return authorizable.fullName || authorizable.id;
						}

						const item = _this.$getItem(o);
						if (item) {
							return item.name;
						}

						return '' + o;
					};

					const a1 = _text(a);
					const b1 = _text(b);
					if (a1 < b1) {
						return -1;
					}
					if (a1 > b1) {
						return 1;
					}
					return 0;
				};
				this.$onChanged = function() {
					return;
				};
			}

			get selection() {
				if (vm.multiple) {
					let l = [];
					for (let identifier of vm.selected.index) {
						l.push(vm.selected.map[identifier]);
					}
					return l;
				}
				if (vm.selected.index.length == 0) {
					return undefined;
				}
				return vm.selected.map[vm.selected.index[0]];
			}
			set selection(objects) {
				if (objects == undefined) {
					objects = [];
				}
				if (!Array.isArray(objects)) {
					objects = [objects];
				}

				vm.selected = {
					'index': [],
					'map': {}
				};

				for (let obj of objects) {
					let id = this.$getIdentifier(obj);
					if (id == undefined) {
						continue;
					}
					vm.selected.index.push(id);
					vm.selected.map[id] = obj;
				}
			}

			get objects() {
				return Object.values(vm.objects.map);
			}
			set objects(objects) {
				if (objects == undefined) {
					objects = [];
				}
				if (!Array.isArray(objects)) {
					objects = [objects];
				}

				vm.objects = {
					'index': [],
					'map': {}
				};

				for (let obj of objects) {
					let id = this.$getIdentifier(obj);
					if (id == undefined) {
						continue;
					}
					vm.objects.index.push(id);
					vm.objects.map[id] = obj;
				}
			}

			set getIdentifier(f) {
				this.$getIdentifier = f;
			}
			set getAuthorizable(f) {
				this.$getAuthorizable = f;
			}
			set getItem(f) {
				this.$getItem = f;
			}
			set getIcon(f) {
				this.$getIcon = f;
			}
			set getLabel(f) {
				this.$getLabel = f;
			}
			set comparator(f) {
				this.$comparator = f;
			}
			set onChanged(f) {
				this.$onChanged = f;
			}
		}
		vm.ui = new UI();
	},
	computed: {
		selectables() {
			let vm = this;
			let l = [];
			for (let id of vm.objects.index) {
				l.push(id);
			}
			l.sort(function(a, b) {
				let a1 = vm.objects.map[a];
				let b1 = vm.objects.map[b];
				return vm.ui.$comparator(a1, b1);
			});
			return l;
		},
	},
	methods: {
		isSelected(id) {
			let vm = this;
			return (vm.selected.index.indexOf(id) != -1);
		},
		onClick(id) {
			let vm = this;
			if (vm.multiple) {
				let i = vm.selected.index.indexOf(id);
				if (i == -1) {
					vm.selected.map[id] = vm.objects.map[id];
					vm.selected.index.push(id);
				} else {
					delete vm.selected.map[id];
					vm.selected.index.splice(i, 1);
				}
			} else {
				if (vm.selected.index.indexOf(id) == -1) {
					let m = {};
					m[id] = vm.objects.map[id];
					vm.selected.map = m;
					vm.selected.index = [id];
				}
			}
		}
	},
	watch: {
		'selected.index': function() {
			let vm = this;
			vm.ui.$onChanged();
		},
	},
}
</script>

<style lang="scss" scoped>
.ItemBadge {
	min-height: 2.5em;
}

.badge-avatar {
	position: relative;
	width: 2.5em;
	height: 2.5em;
	min-width: 2.5em;
	min-height: 2.5em;
	border-radius: 50%;
	background-position: center center;
	background-size: cover;
	background-repeat: no-repeat;

	.color-overlay {
		position: absolute;
		width: .75em;
		height: .75em;
		min-width: .75em;
		min-height: .75em;
		border-radius: 50%;
	}

	.overlay-right-bottom {
		right: 0;
		bottom: 0;
	}
}

.NotFound:not(:first-child) {
	display: none;
}
</style>
