<script lang="ts">
    import {activeBlockchain} from '~/store'
    import type {Token} from '~/stores/tokens'

    import Button from '~/components/elements/button.svelte'
    import Text from '~/components/elements/text.svelte'
    import Input from '~/components/elements/input.svelte'

    import {transferData} from '~/pages/transfer/transfer'
    import {txFee} from '~/pages/transfer/fio'
    import StatusAddress from '~/pages/transfer/status/address.svelte'
    import StatusAccount from '~/pages/transfer/status/account.svelte'
    import StatusQuantity from '~/pages/transfer/status/quantity.svelte'
    import StatusFee from '~/pages/transfer/status/fee.svelte'

    export let handleTransfer: () => void
    export let token: Token

    let memo: string = ''

    function handleKeydown(event: KeyboardEvent) {
        if (event.key === 'Enter') {
            handleConfirm()
        }
    }

    function handleConfirm() {
        transferData.update((data) => ({
            ...data,
            memo,
        }))

        handleTransfer()
    }
</script>

<style type="scss">
    .memo-container {
        padding: 20px 0 0 0;

        span {
            font-weight: bold;
        }
    }
</style>

<svelte:window on:keydown={handleKeydown} />
{#if $transferData.toAddress}
    <StatusAddress editable toAddress={$transferData.toAddress} />
{/if}
{#if $transferData.toAccount}
    <StatusAccount editable toAccount={$transferData.toAccount} />
{/if}
{#if $transferData.quantity}
    <StatusQuantity editable quantity={$transferData.quantity} {token} />
{/if}
{#if $transferData.quantity && $txFee}
    <StatusFee txFee={$txFee} quantity={$transferData.quantity} />
{/if}
{#if $activeBlockchain && $activeBlockchain.id !== 'fio'}
    <div class="memo-container">
        <span>Memo (Optional)</span>
        <br />
        <br />
        <Input name="memo" assumeValid fluid bind:value={memo} placeholder="Memo" />
    </div>
{/if}
<Button fluid style="primary" size="large" formValidation on:action={handleConfirm}>
    <Text>Sign Transaction</Text>
</Button>
