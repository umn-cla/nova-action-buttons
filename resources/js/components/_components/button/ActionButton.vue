<template>
    <a href="#"
       class="d-flex align-center"
       v-bind="actionOptions"
       @click.stop.prevent="fireAction"
       @mouseenter="onMouseEnterTooltip"
       @mouseleave="onMouseLeaveTooltip"
    >

        <!-- Text -->
        <div v-if="text" v-text="text"></div>

        <!-- Icon -->
        <div v-if="hasIcon" :class="{'ml-2': text}">
            <svg v-if="icon && iconPaths[icon]" class="w-5 h-5 inline" fill="none" stroke="currentColor" stroke-width="1.5" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" :d="iconPaths[icon]"></path>
            </svg>
            <span v-if="iconHtml" v-html="iconHtml"></span>
            <img v-if="iconUrl" :alt="title" :src="iconUrl" class="w-6 h-6 inline" />
        </div>

        <!-- Tooltip -->
        <div v-if="tooltipIsVisible && tooltip" class="absolute z-10 left-1/2 -top-1 transform -translate-x-1/2 -translate-y-full px-2 py-1 bg-gray-700 text-white text-xs rounded">
            {{ tooltip }}
        </div>

    </a>
</template>

<script setup>

    // Composables
    import {computed, ref} from 'vue';

    // Heroicon SVG paths for common icons
    const iconPaths = {
        'envelope': 'M21.75 6.75v10.5a2.25 2.25 0 01-2.25 2.25h-15a2.25 2.25 0 01-2.25-2.25V6.75m19.5 0A2.25 2.25 0 0019.5 4.5h-15a2.25 2.25 0 00-2.25 2.25m19.5 0v.243a2.25 2.25 0 01-1.07 1.916l-7.5 4.615a2.25 2.25 0 01-2.36 0L3.32 8.91a2.25 2.25 0 01-1.07-1.916V6.75',
        'check-circle': 'M9 12.75L11.25 15 15 9.75M21 12a9 9 0 11-18 0 9 9 0 0118 0z',
        'x-circle': 'M9.75 9.75l4.5 4.5m0-4.5l-4.5 4.5M21 12a9 9 0 11-18 0 9 9 0 0118 0z',
    };

    // Props
    const props = defineProps({
        field: {type: Object, default: null},
        fireAction: {type: Function, default: null},
    });

    // State
    const tooltipIsVisible = ref(false);

    // Computed
    const title = computed(() => props?.field?.name || props?.field?.title || null);
    const text = computed(() => props?.field?.text || null);
    const tooltip = computed(() => props?.field?.tooltip || props?.field?.action?.name);

    // Computed
    // Styles
    const customStyles = computed(() => props?.field?.styles || []);
    const customClasses = computed(() => props?.field?.classes || []);
    const asToolbarButton = computed(() => props?.field?.asToolbarButton === true);
    const variant = computed(() => props?.field?.variant || 'primary');

    // Computed
    // Icon
    const icon = computed(() => props?.field?.icon || null);
    const iconUrl = computed(() => props?.field?.iconUrl || null);
    const iconHtml = computed(() => props?.field?.iconHtml || null);

    // Computed
    const hasIcon = computed(() => icon?.value !== null || iconHtml?.value !== null || iconUrl?.value !== null);
    const hasTooltip = computed(() => props?.field?.hasTooltip === true);


    // Computed
    // Get action options
    const actionOptions = computed(() => {
        return {
            title: title?.value,
            class: [
                ...(asToolbarButton?.value === true ? toolbarButtonClasses.value : actionButtonClasses.value),
                ...(customClasses.value || [])
            ],
            style: {
                ...(asToolbarButton?.value === true ? null : actionButtonStyles?.value),
                ...(customStyles.value || {}),
            }
        }
    })

    // Computed
    // Get action button classes
    const actionButtonClasses = computed(() => {
        const baseClasses = [
            'flex-shrink-0', 'shadow-sm', 'rounded-md', 'focus:outline-none',
            'focus:ring-2', 'focus:ring-offset-1',
            'text-white', 'inline-flex', 'items-center', 'font-medium',
            'px-3', 'h-9', 'text-sm', 'transition-colors', 'duration-150'
        ];
        
        const variantClasses = {
            primary: ['bg-blue-600', 'hover:bg-blue-700', 'focus:ring-blue-500'],
            success: ['bg-green-600', 'hover:bg-green-700', 'focus:ring-green-500'],
            danger: ['bg-red-600', 'hover:bg-red-700', 'focus:ring-red-500'],
            secondary: ['bg-gray-600', 'hover:bg-gray-700', 'focus:ring-gray-500'],
        };
        
        return [...baseClasses, ...(variantClasses[variant.value] || variantClasses.primary)];
    })

    // Computed
    // Get toolbar button classes
    const toolbarButtonClasses = computed(() => [
        'toolbar-button', 'hover:text-primary-500', 'px-2', 'v-popper--has-tooltip', 'w-10'
    ])

    // Computed
    // Get action button styles
    const actionButtonStyles = computed(() => {
        return {
            margin: '0 2px',
        }
    })

    // Methods
    // Fire mouse actions for tooltip
    const onMouseLeaveTooltip = () => tooltipIsVisible.value = false
    const onMouseEnterTooltip = () => tooltipIsVisible.value = hasTooltip.value;

</script>
