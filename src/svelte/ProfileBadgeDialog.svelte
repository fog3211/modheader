<script>
  import ColorPicker from "./svelte-picker";
  import Dialog, { Title, Content, Actions } from "@smui/dialog";
  import Tab, {Icon, Label as TabLabel} from '@smui/tab';
  import Button, { Label } from "@smui/button";
  import TabBar from '@smui/tab-bar';
  import IconButton from "@smui/icon-button";
  import { mdiContentSave, mdiClose } from "@mdi/js";
  import MdiIcon from "./MdiIcon.svelte";
  import { PRIMARY_COLOR } from "../js/constants";
  import {
    selectedProfile,
    commitChange
  } from "../js/datasource";

  const TABS = [
    {label: 'Background', value: 'backgroundColor'},
    {label: 'Text Color', value: 'textColor'}
  ];
  let dialog;
  let shortTitle;
  let backgroundColor;
  let textColor;
  let shortTitleTextfield;
  let activeTab = TABS[0];

  export function show() {
    backgroundColor = $selectedProfile.backgroundColor;
    textColor = $selectedProfile.textColor;
    shortTitle = $selectedProfile.shortTitle;
    dialog.open();
    shortTitleTextfield.focus();
  }
</script>

<style scoped>
  .profile-badge-preview {
    border-radius: 50%;
    display: inline-block;
    width: 24px;
    height: 24px;
  }

  .profile-badge-preview-text {
    background: none;
    border: none;
    color: white;
    padding-left: 6px;
    font-size: 20px;
    width: 24px;
  }
  
  .color-picker-container {
    margin-top: 10px;
    display: flex;
    justify-content: center;
  }
</style>

<Dialog
  bind:this={dialog}
  class="profile-badge-dialog"
  aria-labelledby="dialog-title"
  aria-describedby="dialog-content">
  <Title id="dialog-title">
    Profile badge
    <IconButton
      aria-label="Close"
      class="dialog-close-button"
      on:click={() => dialog.close()}>
      <MdiIcon size="32" icon={mdiClose} color="#888" />
    </IconButton>
  </Title>
  <Content id="dialog-content">
    <div>Change badge text:
      <span
        class="profile-badge-preview"
        style="background: {backgroundColor}">
        <input class="profile-badge-preview-text"
            bind:this={shortTitleTextfield}
            bind:value={shortTitle}
            style="color: {textColor}"
            type="text"
            maxlength="1">
      </span>
    </div>
    
    <TabBar tabs={TABS} let:tab bind:active={activeTab}>
      <Tab {tab}>
        <TabLabel>{tab.label}</TabLabel>
      </Tab>
    </TabBar>
    <div class="color-picker-container">
      {#if activeTab.value === 'backgroundColor'}
        <ColorPicker
          alpha={false}
          showInfobox={false}
          startColor={$selectedProfile.backgroundColor}
          on:colorchange={(event) => {
            const rgbString = event.detail.hex;
            if (rgbString !== backgroundColor) {
              backgroundColor = rgbString;
            }
          }} />
      {:else}
        <ColorPicker
          alpha={false}
          showInfobox={false}
          startColor={$selectedProfile.textColor}
          on:colorchange={(event) => {
            const rgbString = event.detail.hex;
            if (rgbString !== textColor) {
              textColor = rgbString;
            }
          }} />
      {/if}
    </div>
  </Content>
  <div class="mdc-dialog__actions">
    <Button
      on:click={() => dialog.close()}>
      <MdiIcon
        size="24"
        icon={mdiClose}
        color={PRIMARY_COLOR} />
      <Label class="ml-small">Cancel</Label>
    </Button>
    <Button
      on:click={() => {
        commitChange({
          shortTitle,
          backgroundColor,
          textColor,
        });
        dialog.close();
      }}>
      <MdiIcon
        size="24"
        icon={mdiContentSave}
        color={PRIMARY_COLOR} />
      <Label class="ml-small">Save</Label>
    </Button>
  </div>
</Dialog>

