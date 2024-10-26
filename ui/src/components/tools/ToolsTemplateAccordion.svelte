<script context="module">
    let cachedEditorComponent;
</script>

<script>
    import { scale } from "svelte/transition";
    import tooltip from "@/actions/tooltip";
    import { errors, removeError } from "@/stores/errors";
    import { addInfoToast } from "@/stores/toasts";
    import CommonHelper from "@/utils/CommonHelper";
    import Field from "@/components/base/Field.svelte";
    import Accordion from "@/components/base/Accordion.svelte";
    import { Base64 } from 'js-base64';

    export let key;
    export let title;
    export let config = {};

    let left;
    
    $: right = Changea(left);

    let accordion;
    let editorComponent = cachedEditorComponent;
    let isEditorComponentLoading = false;

    $: hasErrors = !CommonHelper.isEmpty(CommonHelper.getNestedVal($errors, key));

    $: if (!config.enabled) {
        removeError(key);
    }

    export function expand() {
        accordion?.expand();
    }

    export function collapse() {
        accordion?.collapse();
    }

    export function collapseSiblings() {
        accordion?.collapseSiblings();
    }

    async function loadEditorComponent() {
        if (editorComponent || isEditorComponentLoading) {
            return; // already loaded or in the process
        }

        isEditorComponentLoading = true;

        editorComponent = (await import("@/components/base/CodeEditor.svelte")).default;

        cachedEditorComponent = editorComponent;

        isEditorComponentLoading = false;
    }

    function copy(param) {
        CommonHelper.copyToClipboard(param);
        addInfoToast(`Copied ${param} to clipboard`, 2000);
    }

    function Changea(data) {
        console.log('1111', data);
        var rs = "";
        if (data === undefined) {
            return "";
        }
        let tmp = data.split(".");
        console.log(data, tmp, tmp.length);
        if (tmp.length === 3) {
            // let xx = Base64.decode(tmp[1]);
            // rs = JSON.parse(xx);
            rs = Base64.decode(tmp[1]);
        } else {
            // rs = JSON.parse('{"msg":"数据格式不对"}');
            rs = '{"msg":"数据格式不对"}';
        }
        console.log('2222', rs);
        return rs
    }

    loadEditorComponent();
</script>

<Accordion bind:this={accordion} on:expand on:collapse on:toggle {...$$restProps}>
    <svelte:fragment slot="header">
        <div class="inline-flex">
            <i class="ri-draft-line" />
            <span class="txt">{title}</span>
        </div>

        <div class="flex-fill" />

        {#if hasErrors}
            <i
                class="ri-error-warning-fill txt-danger"
                transition:scale={{ duration: 150, start: 0.7 }}
                use:tooltip={{ text: "Has errors", position: "left" }}
            />
        {/if}
    </svelte:fragment>

    <!-- <Field class="form-field required" name="{key}.subject" let:uniqueId>
        <label for={uniqueId}>原文</label>
        <input type="text" id={uniqueId} bind:value={config.subject} spellcheck="false" required />
        <div class="help-block">
            Available placeholder parameters:
            <button
                type="button"
                class="label label-sm link-primary txt-mono"
                on:click={() => copy("{APP_NAME}")}
            >
                {"{APP_NAME}"}
            </button>,
            <button
                type="button"
                class="label label-sm link-primary txt-mono"
                on:click={() => copy("{APP_URL}")}
            >
                {"{APP_URL}"}
            </button>.
        </div>
    </Field>

    <Field class="form-field required" name="{key}.actionUrl" let:uniqueId>
        <label for={uniqueId}>译文</label>
        <input type="text" id={uniqueId} bind:value={config.actionUrl} spellcheck="false" required />
        <div class="help-block">
            Available placeholder parameters:
            <button
                type="button"
                class="label label-sm link-primary txt-mono"
                on:click={() => copy("{APP_NAME}")}
            >
                {"{APP_NAME}"}
            </button>,
            <button
                type="button"
                class="label label-sm link-primary txt-mono"
                on:click={() => copy("{APP_URL}")}
            >
                {"{APP_URL}"}
            </button>,
            <button
                type="button"
                class="label label-sm link-primary txt-mono"
                title="Required parameter"
                on:click={() => copy("{TOKEN}")}
            >
                {"{TOKEN}"}
            </button>.
        </div>
    </Field> -->

    <Field class="form-field required" name="{key}.body" let:uniqueId>
        <label for={uniqueId}>原文</label>

        <textarea
            id={uniqueId}
            class="txt-mono"
            spellcheck="false"
            rows="14"
            required
            bind:value={left}
        />

        <div class="help-block">
            Available placeholder parameters:
            <button
                type="button"
                class="label label-sm link-primary txt-mono"
                on:click={() => copy("{APP_NAME}")}
            >
                {"{APP_NAME}"}
            </button>,
            <button
                type="button"
                class="label label-sm link-primary txt-mono"
                on:click={() => copy("{APP_URL}")}
            >
                {"{APP_URL}"}
            </button>,
            <button
                type="button"
                class="label label-sm link-primary txt-mono"
                on:click={() => copy("{TOKEN}")}
            >
                {"{TOKEN}"}
            </button>,
            <button
                type="button"
                class="label label-sm link-primary txt-mono"
                title="Required parameter"
                on:click={() => copy("{ACTION_URL}")}
            >
                {"{ACTION_URL}"}
            </button>.
        </div>
    </Field>

    <Field class="form-field m-0 required" name="{key}.body" let:uniqueId>
        <label for={uniqueId}>译文</label>

        <textarea
            id={uniqueId}
            class="txt-mono"
            spellcheck="false"
            rows="14"
            required
            bind:value={right}
        />

        <div class="help-block">
            Available placeholder parameters:
            <button
                type="button"
                class="label label-sm link-primary txt-mono"
                on:click={() => copy("{APP_NAME}")}
            >
                {"{APP_NAME}"}
            </button>,
            <button
                type="button"
                class="label label-sm link-primary txt-mono"
                on:click={() => copy("{APP_URL}")}
            >
                {"{APP_URL}"}
            </button>,
            <button
                type="button"
                class="label label-sm link-primary txt-mono"
                on:click={() => copy("{TOKEN}")}
            >
                {"{TOKEN}"}
            </button>,
            <button
                type="button"
                class="label label-sm link-primary txt-mono"
                title="Required parameter"
                on:click={() => copy("{ACTION_URL}")}
            >
                {"{ACTION_URL}"}
            </button>.
        </div>
    </Field>
</Accordion>
