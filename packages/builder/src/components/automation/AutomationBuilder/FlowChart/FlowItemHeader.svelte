<script>
  import { automationStore } from "builderStore"
  import { Icon, Body, Detail, StatusLight } from "@budibase/bbui"
  import { externalActions } from "./ExternalActions"

  export let block
  export let blockComplete
  export let showTestStatus = false
  export let showParameters = {}

  $: testResult =
    $automationStore.selectedAutomation?.testResults?.steps.filter(step =>
      block.id ? step.id === block.id : step.stepId === block.stepId
    )
  $: isTrigger = block.type === "TRIGGER"

  async function onSelect(block) {
    await automationStore.update(state => {
      state.selectedBlock = block
      return state
    })
  }
</script>

<div class="blockSection">
  <div
    on:click={() => {
      blockComplete = !blockComplete
      showParameters[block.id] = blockComplete
    }}
    class="splitHeader"
  >
    <div class="center-items">
      {#if externalActions[block.stepId]}
        <img
          alt={externalActions[block.stepId].name}
          width="28px"
          height="28px"
          src={externalActions[block.stepId].icon}
        />
      {:else}
        <svg
          width="28px"
          height="28px"
          class="spectrum-Icon"
          style="color:grey;"
          focusable="false"
        >
          <use xlink:href="#spectrum-icon-18-{block.icon}" />
        </svg>
      {/if}
      <div class="iconAlign">
        {#if isTrigger}
          <Body size="XS">When this happens:</Body>
        {:else}
          <Body size="XS">Do this:</Body>
        {/if}

        <Detail size="S">{block?.name?.toUpperCase() || ""}</Detail>
      </div>
    </div>
    <div class="blockTitle">
      {#if showTestStatus && testResult && testResult[0]}
        <div style="float: right;">
          <StatusLight
            positive={isTrigger || testResult[0].outputs?.success}
            negative={!testResult[0].outputs?.success}
            ><Body size="XS"
              >{testResult[0].outputs?.success || isTrigger
                ? "Success"
                : "Error"}</Body
            ></StatusLight
          >
        </div>
      {/if}
      <div
        style="margin-left: 10px; margin-bottom: var(--spacing-xs);"
        on:click={() => {
          onSelect(block)
        }}
      >
        <Icon name={blockComplete ? "ChevronUp" : "ChevronDown"} />
      </div>
    </div>
  </div>
</div>

<style>
  .center-items {
    display: flex;
    align-items: center;
  }
  .splitHeader {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
  .iconAlign {
    padding: 0 0 0 var(--spacing-m);
    display: inline-block;
  }

  .blockSection {
    padding: var(--spacing-xl);
  }

  .blockTitle {
    display: flex;
    align-items: center;
  }
</style>
