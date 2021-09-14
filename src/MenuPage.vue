<template>
  <div class="container">
    <div class="row">
      <div class="col-6">
        <div class="ibox">
          <div class="ibox-title">
            <h5>Menu builder</h5>
            <div class="add-button">
              <button
                class="btn btn-sm btn-info m-t-n-xs m-l-xs save-changes"
                type="button"
                style="float: right"
              >
                <strong>Save changes</strong>
              </button>
              <button
                class="btn btn-sm btn-primary m-t-n-xs"
                type="button"
                style="float: right"
                data-toggle="modal"
                data-target="#addModal"
              >
                <strong>Add menu</strong>
              </button>
            </div>
          </div>
          <div class="ibox-content">
            <div class="dd" id="nestable">
              <ol class="dd-list">
                <li
                  v-for="menu in menuList"
                  :key="menu.id"
                  :data-id="menu.id"
                  class="dd-item"
                  :class="{
                    'dd-collapsed': menuListMap[menu.id].actions.isToDelete,
                  }"
                >
                  <!-- parent -->
                  <div
                    class="dd-handle"
                    :class="{
                      deleted: menuListMap[menu.id].actions.isToDelete,
                      moved: menuListMap[menu.id].actions.isMoved,
                      edited: menuListMap[menu.id].actions.isEdited,
                    }"
                  >
                    {{ menu.title }} - {{ menu.id}}
                  </div>
                  <span class="deleteIcon" @click="toDelete(menu.id)">
                    <i class="fas fa-trash pull-right"></i>
                  </span>
                  <span class="editIcon" @click="editMenu(menu)">
                    <i class="fas fa-edit pull-right"></i>
                  </span>
                  <ol class="dd-list" v-if="menu.columns">
                    <li
                      class="dd-item"
                      v-for="col in menu.columns"
                      :key="col.type"
                      :data-id="col.id"
                      :class="{
                        'dd-collapsed': menuListMap[col.id].actions.isToDelete,
                      }"
                    >
                      <div
                        class="dd-handle"
                        :class="{
                          deleted: menuListMap[col.id].actions.isToDelete,
                          moved: menuListMap[col.id].actions.isMoved,
                          edited: menuListMap[col.id].actions.isEdited,
                        }"
                      >
                        {{ col.title }} -{{ col.id}}
                      </div>

                      <span class="deleteIcon" @click="toDelete(col.id)">
                        <i class="fas fa-trash pull-right"></i>
                      </span>
                      <span class="editIcon" @click="editMenu(col)">
                        <i class="fas fa-edit pull-right"></i>
                      </span>
                      <ol class="dd-list" v-if="col.subItems">
                        <li
                          v-for="subItem in col.subItems"
                          :key="subItem.id"
                          :data-id="subItem.id"
                          class="dd-item dd-nochildren"
                          :class="{
                            'dd-collapsed':
                              menuListMap[subItem.id].actions.isToDelete,
                          }"
                        >
                          <div
                            class="dd-handle"
                            :disabled="
                              menuListMap[subItem.id].actions.isToDelete
                            "
                            :class="{
                              deleted:
                                menuListMap[subItem.id].actions.isToDelete,
                              moved: menuListMap[subItem.id].actions.isMoved,
                              edited: menuListMap[subItem.id].actions.isEdited,
                            }"
                          >
                            {{ subItem.title }} - {{subItem.id}}
                          </div>
                          <span
                            class="deleteIcon"
                            @click="toDelete(subItem.id)"
                          >
                            <i class="fas fa-trash pull-right"></i>
                          </span>
                          <span class="editIcon" @click="editMenu(subItem)">
                            <i class="fas fa-edit pull-right"></i>
                          </span>
                        </li>
                      </ol>
                    </li>
                  </ol>
                </li>
                <!--START EDIT MODAL -->
                <div
                  class="modal inmodal fade"
                  id="editModal"
                  tabindex="-1"
                  role="dialog"
                  style="display: none"
                  aria-hidden="true"
                >
                  <div class="modal-dialog">
                    <div class="modal-content">
                      <div class="modal-body">
                        <div class="modal-row">
                          <div class="modal-information">
                            <h3 class="m-t-none m-b">Edit</h3>
                            <div class="form-group">
                              <label>Title</label>
                              <input
                                type="text"
                                class="form-control"
                                @change.prevent="changeIsEdited(selectedItem)"
                                v-model="selectedItem.title"
                              />
                            </div>
                            <div class="form-group">
                              <label>Link</label>
                              <input
                                type="text"
                                class="form-control"
                                v-model="selectedItem.link"
                              />
                            </div>

                            <div>
                              <button
                                class="btn btn-sm btn-primary m-t-n-xs m-l"
                                data-dismiss="modal"
                                type="button"
                                style="float: right"
                              >
                                <strong>close</strong>
                              </button>
                            </div>
                          </div>
                        </div>
                      </div>
                    </div>
                  </div>
                </div>
                <!-- END MODAL -->
                <!--START EDIT MODAL -->
                <div
                  class="modal inmodal fade"
                  id="addModal"
                  tabindex="-1"
                  role="dialog"
                  style="display: none"
                  aria-hidden="true"
                >
                  <div class="modal-dialog">
                    <div class="modal-content">
                      <div class="modal-body">
                        <div class="modal-row">
                          <div class="modal-information">
                            <h3 class="m-t-none m-b">Add menu</h3>
                            <div class="form-group">
                              <label>Title</label>
                              <input
                                type="text"
                                class="form-control"
                                v-model="addMenuItem.title"
                              />
                            </div>
                            <div class="form-group">
                              <label>Link</label>
                              <input
                                type="text"
                                class="form-control"
                                v-model="addMenuItem.link"
                              />
                            </div>

                            <div>
                              <button
                                class="btn btn-sm btn-primary m-t-n-xs m-l"
                                data-dismiss="modal"
                                type="button"
                                style="float: right"
                              >
                                <strong>close</strong>
                              </button>
                              <button
                                class="btn btn-sm btn-primary m-t-n-xs"
                                type="button"
                                style="float: right"
                                @click="saveAddMenu"
                                :disabled="addMenuItem.title.length < 5"
                              >
                                <strong>Save</strong>
                              </button>
                            </div>
                          </div>
                        </div>
                      </div>
                    </div>
                  </div>
                </div>
                <!-- END MODAL -->
              </ol>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import mockData from "./mockdata";

export default {
  name: "Menu-Page",
  components: {},
  data: () => {
    return {
      menuList: [],
      menuListMap: {},
      menuListUpdated: [],
      addMenuItem: {
        title: "",
        link: null,
        actions: {
          isToDelete: false,
          isMoved: false,
          isEdited: false,
        },
      },
      selectedItem: {},
      draggedItem: {},
    };
  },
  computed: {
    prepareMenuMap() {
      const list = [];
      this.menuList.forEach((item) => {
        if (item.columns && item.columns.length > 0) {
          list.push({ ...item, columns: null });
          item.columns.forEach((child) => {
            if (child.subItems && child.subItems.length > 0) {
              list.push({ ...child, subItems: null });
              child.subItems.forEach((subItem) => {
                list.push(subItem);
              });
            } else {
              list.push(child);
            }
          });
        } else {
          list.push(item);
        }
      });
      this.menuListMap = list.reduce(
        (map, item) => ((map[item.id] = item), map),
        {}
      );
    },
  },
  methods: {
    async getData() {
      const data = JSON.parse(localStorage.getItem('data'));
      this.menuList = data && data.length > 0 ? data : mockData;
      this.prepareMenuMap;
    },
    changeIsEdited(item){
      console.log(item);
        item.actions.isEdited = true;
    },
    toDelete(id) {
      this.menuListMap[id].actions.isToDelete =
        !this.menuListMap[id].actions.isToDelete;
      this.handleChange();
    },

    openAddModal() {
      $("#addModal").modal("show");
    },
    saveAddMenu() {
      this.addMenuItem.id = Date.now();
      this.menuList.push(this.addMenuItem);
      this.prepareMenuMap;
      this.addMenuItem = {
        title: "",
        link: null,
        id: Date.now(),
        actions: {
          isToDelete: false,
          isMoved: false,
          isEdited: false,
        },
      };
      $("#addModal").modal("hide");
      this.handleChange();
    },
    editMenu(menuObj) {
      this.selectedItem = menuObj;

      $("#editModal").modal("show",()=>{
        console.log('aa')
      });

    },
    saveEditMenu() {},
    getItemsMap(nonOrderedList, resetActions = false) {
      const list = [];
      nonOrderedList.forEach((item) => {
        if(item.actions.isToDelete) return
        item.actions = resetActions ? this.resetActions(item) : item.actions;
        if (item.columns && item.columns.length > 0) {
          list.push({ ...item, columns: null });
          item.columns.forEach((child) => {
            if(child.actions.isToDelete) return
            child.actions = resetActions ? this.resetActions(child) : child.actions;
            if (child.subItems && child.subItems.length > 0) {
              list.push({ ...child, subItems: null });
              child.subItems.forEach((subItem) => {
                if(subItem.actions.isToDelete) return
                subItem.actions = resetActions ? this.resetActions(subItem) : subItem.actions;
                list.push(subItem);
              });
            } else {
              list.push(child);
            }
          });
        } else {
          list.push(item);
        }
      });
      return list.reduce((map, item) => ((map[item.id] = item), map), {});
    },
    getNewDataStructure(map, idsList) {
      const updatedList = [];
      idsList.forEach((item) => {
        const itemToPush = { ...item };
        if (item.children) {
          itemToPush.columns = item.children.reduce((res, sub) => {
            let subItemsToPush = {};
            if(map[sub.id]){
              subItemsToPush = {...map[sub.id]}
              if (sub.children) {
                  subItemsToPush.subItems = sub.children.reduce((subItems, grandChild) => {
                    if(map[grandChild.id]){
                      return [...subItems, map[grandChild.id]]
                    }
                  return [...subItems];
                }, []);
              }
              delete subItemsToPush.children;
              return [...res, {...subItemsToPush}]
            } 
            return [...res];
          }, []);
          // item.children.map((sub) => {
          //   if(!map[sub.id]) return;
          //   const subItemsToPush = { ...map[sub.id] };
          //   if (sub.children) {
          //     subItemsToPush.subItems = sub.children.filter((grandChild) => {
          //       return grandChild && map[grandChild.id];
          //     });
          //   }
          //   delete subItemsToPush.children;
          //   return subItemsToPush;
          // });
        }
        if(map[itemToPush.id]){
          updatedList.push({
            ...map[itemToPush.id],
            columns: itemToPush.columns,
          });
        }
      });
      return updatedList;
    },
    resetActions(item){
      return item.actions = {
        isToDelete: false,
        isMoved: false,
        isEdited: false,
      }
    },
    handleChange(e) {
      if (this.draggedItem.element) {
        this.draggedItem.currentLocation = Object.values(
          this.draggedItem.element.offset()
        ).reduce((sum, value) => sum + value, 0);
        const elementId = this.draggedItem.element[0].dataset.id;
        if (
          this.draggedItem.currentLocation !== this.draggedItem.prevLocation
        ) {
          this.changeItemColorWhenMoved(elementId);
        }
      }
      // const list = $("#nestable").nestable("serialize");
      // const flattedList = this.listToFlat(this.menuList);
      // const newStructure = this.getNewDataStructure(flattedList, list);
      // this.menuListUpdated = newStructure;
    },
    changeItemColorWhenMoved(id) {
      this.menuListMap[id].actions.isMoved = true;
    },
    saveChanges() {
      const list = $("#nestable").nestable("serialize");
      console.log('list:', list);
      const flattedList = this.getItemsMap(this.menuList, true);
      console.log('flattedList',flattedList);
      const newStructure = this.getNewDataStructure(flattedList, list);
      console.log('newStructure',newStructure);
      this.menuListUpdated = newStructure;
      localStorage.setItem('data', JSON.stringify(newStructure));
    },
  },
  async mounted() { 
    this.getData();
    const initDrag = () => {};
    $(() => {
      $("#nestable")
        .nestable({
          group: 1,
          onDragStart: (l, e, p) => {
            this.draggedItem.element = $(e[0]);
            this.draggedItem.prevLocation = Object.values(
              this.draggedItem.element.offset()
            ).reduce((sum, value) => sum + value, 0);

          },
          beforeDragStop: function (l, e, p) {
          },
        })

        .on("change", this.handleChange);
      $(".dd").nestable("expandAll");
    });
    initDrag();
    const initSaveChangesAlert = () => {
      $(".save-changes").click(() => {
        swal(
          {
            title: "Are you sure?",
            type: "warning",
            showCancelButton: true,
            confirmButtonColor: "#DD6B55",
            confirmButtonText: "Yes!",
            closeOnConfirm: false,
          },
          (result) => {
            if (result) {
              swal("Saved!", "", "success");
              this.saveChanges();
            } else {
              console.log("canceled...");
            }
          }
        );
      });
    };
    initSaveChangesAlert();
  },
};
</script>

<style>
.dd-handle {
  cursor: move !important;
}
.deleteIcon {
  position: relative;
  bottom: 27px;
  right: 10px;
  cursor: pointer;
}
.editIcon {
  position: relative;
  bottom: 27px;
  right: 20px;
  cursor: pointer;
}

.modal-content {
  display: flex;
  flex-direction: column;
  background-clip: padding-box;
  background-color: #ffffff;
  border: 1px solid rgba(0, 0, 0, 0);
  border-radius: 4px;
  box-shadow: 0 1px 3px rgb(0 0 0 / 30%);
  outline: 0 none;
  position: relative;
}
.form-group input {
  width: 100%;
}
.add-button {
  padding: 5px 6px 20px 20px;
}
.deleted {
  text-decoration: line-through;
  filter: blur(0.5px);
}
.moved {
  background-color: #f8ac59 !important;
}
.moved:hover {
  background-color: #f8ac59 !important;
}
.edited {
  background-color: #23c6c8 !important;
}
.edited:hover {
  background-color: #23c6c8 !important;
}
</style>