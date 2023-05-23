<script>
    // export the timer cpomponent
    export let Timer;
</script>
<div>
    <div class="timer">
        <div class="timer__time">
            {#if $time > 0}
                {#if $time < 10}
                    0{$time}
                {:else}
                    {$time}
                {/if}
            {:else}
                00
            {/if}
        </div>
        <div class="timer__text">
            {#if $time == 1}
                second
            {:else}
                seconds
            {/if}
        </div>
    </div>
</div>
