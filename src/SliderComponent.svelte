<script>
    import { getIcon } from '../../scripts/scripts.js';

    function getHelixIcon(icon) {
        // if (icon === 'star') {
        //     return '<svg xmlns="http://www.w3.org/2000/svg" class="icon icon-star"><use href="/express/icons/ccx-sheet_44.svg#star44"></use></svg>'
        // }
        // return '<img src="#"/>';
        return getIcon(icon);
    }

    const ratings = [
        {
            class: 'one-star',
            img: getHelixIcon('emoji-angry-face'),
            text: 'Disappointing',
            textareaLabel: "We're sorry to hear that. What went wrong?",
            textareaInside: 'Your feedback (Required)',
            feedbackRequired: true
        },
        {
            class: 'two-stars',
            img: getHelixIcon('emoji-thinking-face'),
            text: 'Insufficient',
            textareaLabel: 'We value your feedback. How can we improve?',
            textareaInside: 'Your feedback (Required)',
            feedbackRequired: true
        },
        {
            class: 'three-stars',
            img: getHelixIcon('emoji-upside-down-face'),
            text: 'Satisfied',
            textareaLabel: 'Satisfied is good, but what would make us great?',
            textareaInside: 'Your feedback (Optional)',
            feedbackRequired: false
        },
        {
            class: 'four-stars',
            img: getHelixIcon('emoji-smiling-face'),
            text: 'Helpful',
            textareaLabel: 'Was there more we could do to be better?',
            textareaInside: 'Your feedback (Optional)',
            feedbackRequired: false
        },
        {
            class: 'five-stars',
            img: getHelixIcon('emoji-star-struck'),
            text: 'Amazing',
            textareaLabel: "That's great. Could you tell us what you loved?",
            textareaInside: 'Your feedback (Optional)',
            feedbackRequired: false
        }
    ];

    let selectedRating = ratings.at(4);
    let star = getHelixIcon('star');
    let active = false;
    let sliderValue = 4;

    let rangeSliderRef;
    let sliderFillRef;
    let toolTipRef;

    function _calculateRating(value) {
        const roundedValue = Math.round(value);
        active = true;
        selectedRating = ratings.at(roundedValue - 1);
        active = true;
        return roundedValue;
    }

    function _onRangeInput(e) {
        const { value } = e.target;
        _calculateRating(value);
        _updateToolTip(value);
    }

    function _onRangeChange(e) {
        const roundedValue = _calculateRating(e.target.value);
        sliderValue = roundedValue;
        _updateToolTip(roundedValue);
    }

    function _onStarsClick() {
        const { value } = this;
        _calculateRating(value);
        _updateToolTip(value);
    }

    function _onRangeTouchStart() {
        toolTipRef.style.transition = 'none';
        sliderFillRef.style.transition = 'none';
    }

    function _onRangeTouchEnd() {
        toolTipRef.style.transition = 'left .3s, right .3s';
        sliderFillRef.style.transition = 'width .3s';
    }

    function _onSubmit(e) {
        e.preventDefault();
        this.submitted = true;
    }

    function _renderStar(count) {
        let stars = '';
        for (let index = 0; index < count; index++) {
            stars += star;
        }
        return stars;
    }

    function _updateToolTip(value) {
        const rangeSlider = rangeSliderRef;
        const toolTip = toolTipRef;
        const sliderFill = sliderFillRef;
        const thumbWidth = 60;
        const pos =
            (value - rangeSlider.getAttribute('min')) /
            (rangeSlider.getAttribute('max') - rangeSlider.getAttribute('min'));
        const thumbCorrect = thumbWidth * (pos - 0.25) * -1 - 0.1;
        const titlePos =
            pos * rangeSlider.offsetWidth - thumbWidth / 4 + thumbCorrect;
        toolTip.style.left = `${titlePos}px`;
        sliderFill.style.width = `${titlePos + thumbWidth / 2}px`;
    }
</script>

<main>
    <div class="ratings block {active && selectedRating.class}">
        <h2 id="rate-our-quick-action">
            Rate our Quick Action<span class="rating-stars"
                >{@html _renderStar(ratings.length)}</span
            >
        </h2>
        <form>
            <div class="slider">
                <div class="tooltip" bind:this={toolTipRef}>
                    <span class="tooltip--text">{selectedRating.text}</span>
                    <div class="tooltip--image">{@html selectedRating.img}</div>
                </div>
                <input
                    bind:this={rangeSliderRef}
                    type="range"
                    min="1"
                    max={ratings.length}
                    step="0.001"
                    aria-labelledby="rate-our-quick-action"
                    bind:value={sliderValue}
                    on:change={_onRangeChange}
                    on:input={_onRangeInput}
                    on:mousedown={_onRangeTouchStart}
                    on:touchstart={_onRangeTouchStart}
                    on:mouseup={_onRangeTouchEnd}
                    on:touchend={_onRangeTouchEnd}
                />
                <div class="slider-fill" bind:this={sliderFillRef} />
            </div>
            <div class="slider-bottom">
                {#each [...ratings] as item, i}
                    <div>
                        <div class="vertical-line">
                            <button
                                type="button"
                                aria-label={i + 1}
                                class="stars {item.class}"
                                value={i + 1}
                                on:click={_onStarsClick}
                            >
                                {@html _renderStar(i + 1)}
                            </button>
                        </div>
                    </div>
                {/each}
            </div>
            <div class="slider-comment">
                <label for="comment">{selectedRating.textareaLabel}</label>
                <textarea
                    id="comment"
                    name="comment"
                    rows="5"
                    placeholder={selectedRating.textareaInside}
                    required={selectedRating.feedbackRequired}
                />
                <input
                    type="submit"
                    value="Submit rating"
                    on:submit={_onSubmit}
                />
            </div>
        </form>
    </div>
</main>

<style>
    main {
        text-align: center;
        padding: 1em;
        max-width: 240px;
        margin: 0 auto;
    }

    @media (min-width: 640px) {
        main {
            max-width: none;
        }
    }

    .ratings-container.section-wrapper {
        background: var(--color-gray-100);
        padding-top: 80px;
        padding-bottom: 80px;
    }
    .ratings {
        text-align: center;
        max-width: 265px;
        margin: 0 auto;
    }
    .ratings .rating-stars {
        color: var(--color-info-accent);
        margin-top: 20px;
    }
    .ratings .rating-stars:global(svg) {
        height: 30px;
        width: 30px;
        transform: translateY(2px);
        margin: 0 2px;
    }
    .ratings .slider .tooltip {
        position: absolute;
        background: var(--color-white);
        height: 60px;
        width: 60px;
        border-radius: 50%;
        top: 50%;
        transform: translate(2px, -50%);
        box-sizing: border-box;
        white-space: nowrap;
        text-align: center;
        box-shadow: 0px 1px 12px rgba(0, 0, 0, 0.3);
        pointer-events: none;
        -moz-user-select: none;
        -webkit-user-select: none;
        -ms-user-select: none;
        user-select: none;
        display: flex;
        justify-content: center;
        align-items: center;
        left: 82.2%;
        z-index: 3;
        transition: left 0.3s, right 0.3s;
    }
    .ratings .slider-fill {
        min-width: 30px;
        height: 30px;
        width: 86.625%;
        position: absolute;
        top: 0;
        left: 0;
        background: var(--color-info-accent);
        pointer-events: none;
        border-radius: 60px;
        z-index: 2;
        transition: width 0.3s;
    }
    .ratings .slider .tooltip > div {
        position: relative;
    }
    .ratings .slider .tooltip--text {
        position: absolute;
        background: var(--color-white);
        top: -80px;
        left: 50%;
        transform: translateX(-50%);
        box-shadow: 0px 1px 12px rgba(0, 0, 0, 0.3);
        padding: 12px 20px;
        border-radius: 8px;
        font-size: 14px;
    }
    .ratings:not(.one-star):not(.two-stars):not(.three-stars):not(.four-stars):not(.five-stars)
        .slider
        .tooltip--text {
        display: none;
    }
    .ratings .slider .tooltip--image,
    .ratings .slider .tooltip--image :global(img) {
        height: 24px;
        width: 24px;
        object-fit: contain;
    }
    .ratings form {
        padding-top: 5rem;
        width: 100%;
        /* adds space for the absolute-positioned tooltip */
    }
    .ratings .slider {
        position: relative;
        box-sizing: border-box;
        width: 100%;
        margin: 0;
    }
    .ratings .slider-bottom {
        display: flex;
        justify-content: space-between;
        padding: 32px 27px 80px 30px;
    }
    .ratings .slider-bottom .vertical-line {
        height: 20px;
        border-left: 2px solid var(--color-gray-400);
        position: relative;
    }
    .ratings .slider-bottom .stars {
        display: flex;
        position: absolute;
        left: calc(50% - 2px);
        transform: translateX(-50%);
        top: 60px;
        color: var(--color-info-accent-light);
        cursor: pointer;
        padding: 2px;
        background: transparent;
        border: none;
        white-space: nowrap;
    }
    .ratings .slider-bottom .stars :global(svg) {
        height: 24px;
        width: 24px;
        margin: 0 1px;
    }
    .ratings .slider .tooltip {
        transition: left 0.3s, right 0.3s;
    }
    @media (max-width: 899px) {
        .ratings .slider-bottom {
            padding: 20px 25px 50px 28px;
        }
        .ratings .slider-bottom .vertical-line {
            height: 5px;
            border-left: 2px solid var(--color-gray-300);
        }
        .ratings .slider-bottom .vertical-line .stars {
            top: 30px;
        }
        .ratings
            .slider-bottom
            .vertical-line:not(:first-child):not(:last-child)
            .stars {
            display: none;
        }
        .ratings .rating-stars {
            display: block;
            width: 100%;
        }
        .ratings .slider .tooltip {
            left: 67%;
        }
        .ratings .slider-fill {
            width: 76%;
        }
        .ratings .slider-bottom .stars :global(svg) {
            height: 20px;
            width: 20px;
        }
        .ratings.one-star .slider-bottom .vertical-line .stars,
        .ratings.two-stars .slider-bottom .vertical-line .stars,
        .ratings.three-stars .slider-bottom .vertical-line .stars,
        .ratings.four-stars .slider-bottom .vertical-line .stars,
        .ratings.five-stars .slider-bottom .vertical-line .stars {
            display: none;
        }
        .ratings.one-star .slider-bottom .vertical-line .stars.one-star,
        .ratings.two-stars .slider-bottom .vertical-line .stars.two-stars,
        .ratings.three-stars .slider-bottom .vertical-line .stars.three-stars,
        .ratings.four-stars .slider-bottom .vertical-line .stars.four-stars,
        .ratings.five-stars .slider-bottom .vertical-line .stars.five-stars {
            color: var(--color-info-accent);
            display: block;
        }
        .ratings .slider .tooltip--text {
            top: -68px;
            padding-top: 10px;
            padding-bottom: 10px;
        }
        .ratings form {
            padding-top: 4.5rem;
        }
    }
    .ratings.one-star .slider-bottom .stars.one-star,
    .ratings.two-stars .slider-bottom .stars.two-stars,
    .ratings.three-stars .slider-bottom .stars.three-stars,
    .ratings.four-stars .slider-bottom .stars.four-stars,
    .ratings.five-stars .slider-bottom .stars.five-stars {
        color: var(--color-info-accent);
        display: block;
    }
    .ratings .slider-comment {
        height: 0px;
        overflow: hidden;
        transition: height 0.4s;
    }
    .ratings.one-star .slider-comment,
    .ratings.two-stars .slider-comment,
    .ratings.three-stars .slider-comment,
    .ratings.four-stars .slider-comment,
    .ratings.five-stars .slider-comment {
        height: 336px;
    }
    .ratings .slider-comment label {
        font-family: var(--body-font-family);
        font-size: var(--body-font-size-xl);
        line-height: var(--body-line-height);
        font-weight: 700;
        margin: 0;
        padding: 0;
        text-align: center;
        width: 100%;
        box-sizing: border-box;
        display: block;
        padding-top: 32px;
    }

    /* Custom styling for the native range input, with cross-browser compatibility */
    .ratings form [type='range'] {
        -webkit-appearance: none;
        background: transparent;
        margin: 0;
        width: 100%;
        height: 30px;
        border-radius: 60px;
        cursor: pointer;
        background: var(--color-info-accent-light);
        -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
    }
    .ratings form [type='range']::-moz-focus-outer {
        border: 0;
    }
    .ratings form [type='range']:focus {
        outline: 0;
    }
    .ratings form [type='range']:focus-visible {
        outline: 2px solid rgba(0, 0, 0, 0.55);
        outline-offset: -2px;
    }
    .ratings form [type='range']:focus-visible + .slider-fill {
        outline: 2px solid rgba(0, 0, 0, 0.55);
        outline-offset: -2px;
    }
    .ratings form [type='range']::-webkit-slider-runnable-track {
        cursor: pointer;
        transition: all 0.2s ease;
        border-radius: 60px;
        height: 30px;
        width: 100%;
        box-shadow: 0 0 0 rgba(0, 0, 0, 0);
        background: var(--color-info-accent-light);
        border: 0 solid rgba(0, 0, 0, 0);
        border-radius: 60px;
    }
    .ratings form [type='range']::-webkit-slider-thumb {
        box-shadow: 0 0 0 rgba(0, 0, 0, 0);
        background: rgba(0, 0, 0, 0);
        border: 0px solid rgba(0, 0, 0, 0);
        border-radius: 60px;
        box-sizing: border-box;
        cursor: pointer;
        height: 60px;
        width: 60px;
        -webkit-appearance: none;
        margin-top: -15px;
    }
    .ratings form [type='range']::-moz-range-track {
        cursor: pointer;
        transition: all 0.2s ease;
        border-radius: 60px;
        height: 30px;
        width: 100%;
        box-shadow: 0 0 0 rgba(0, 0, 0, 0);
        background: var(--color-info-accent-light);
        border: 0 solid rgba(0, 0, 0, 0);
        border-radius: 60px;
        height: 15px;
    }
    .ratings form [type='range']::-moz-range-thumb {
        box-shadow: 0 0 0 rgba(0, 0, 0, 0);
        background: rgba(0, 0, 0, 0);
        border: 0px solid rgba(0, 0, 0, 0);
        border-radius: 60px;
        box-sizing: border-box;
        cursor: pointer;
        height: 60px;
        width: 60px;
    }
    .ratings h2 {
        margin-left: auto;
        margin-right: auto;
        max-width: 235px;
    }

    .ratings .slider-comment textarea {
        resize: none;
        border: 2px solid var(--color-gray-400);
        border-radius: 6px;
        padding: 10px;
        font-family: var(--body-font-family);
        font-size: var(--body-font-size-m);
        color: var(--body-color);
        margin: 16px 0;
        background: var(--color-white);
        width: 100%;
        box-sizing: border-box;
        text-align: left;
    }
    .ratings form [type='submit'] {
        -webkit-appearance: none;
        -moz-appearance: none;
        appearance: none;
        font-family: var(--body-font-family);
        font-size: var(--body-font-size-m);
        line-height: var(--body-line-height);
        color: var(--body-color);
        font-weight: 700;
        font-style: normal;
        text-align: center;
        cursor: pointer;
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
        display: inline-block;
        margin: 0;
        padding: 13px 1.5em 13px 1.5em;
        border-radius: 27px;
        transition: background-color 0.3s, color 0.3s, border 0.3s;
        border: 2px solid var(--color-gray-200);
        background: var(--color-gray-200);
    }
    .ratings form [type='submit']:hover {
        background-color: var(--color-gray-300);
        border-color: var(--color-gray-300);
    }
    .ratings form [type='submit']:active {
        background-color: var(--color-gray-400);
        border-color: var(--color-gray-400);
    }
    .ratings form [type='submit']:focus {
        background-color: var(--color-gray-400);
        border-color: var(--color-gray-400);
        outline: none;
        box-shadow: 0 0 0 2px var(--color-white),
            0 0 0 4px var(--color-gray-400);
    }
    .ratings form textarea:required:placeholder-shown ~ [type='submit'] {
        background-color: #f0f0f0;
        border-color: #f0f0f0;
        color: #b9b9b9;
        outline: none;
        box-shadow: none;
        cursor: default;
        -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
    }
    @media (min-width: 900px) {
        .ratings .cannot-rate {
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .ratings .cannot-rate .button {
            padding-top: 8px;
            padding-bottom: 8px;
            margin-left: 1.5rem;
        }
        .ratings .rating-stars {
            margin: 0 20px;
        }
        .ratings .slider-bottom .stars {
            color: var(--color-gray-200);
        }
        .ratings h2 {
            margin-left: 20px;
            max-width: unset;
        }

        .ratings .slider-comment label {
            padding-top: 50px;
        }

        .ratings.one-star .slider-comment,
        .ratings.two-stars .slider-comment,
        .ratings.three-stars .slider-comment,
        .ratings.four-stars .slider-comment,
        .ratings.five-stars .slider-comment {
            height: 336px;
        }
        .ratings .slider-comment {
            padding: 0 30px;
        }
        .ratings {
            max-width: 750px;
        }
    }
</style>
