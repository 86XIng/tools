<template>
    <div id="app">
        <div class="container">
            <input type="text" v-model="word" />
            <br />
            <select placeholder="sort" v-model="sort">
                <option value="ASC">ASC</option>
                <option value="DESC">DESC</option>
                <option value="AUTO">AUTO</option>
                <option value="RAW">RAW</option>
            </select>

            <select
                placeholder="sort"
                v-model="multiple"
                style="margin-left: 5px"
            >
                <option value="ALL">ALL</option>
                <option value="ANY">ANY</option>
            </select>

            <input
                placeholder="分隔符"
                style="width: 50px; margin-left: 5px"
                type="text"
                v-model="separator"
            />
            <br />
            <textarea
                style="margin-top: 5px; height: 120px; width: 300px"
                type="text"
                v-model="list"
            ></textarea>
            <br />
            <button @click="match(false)">匹配</button>
            &nbsp;
            <button @click="match(true)">高级匹配</button>
            <br />
            <p v-if="advance">
                匹配结果： [
                <span v-for="(item, idx) in result" :key="item">
                    "<span
                        v-for="(key, index) in item"
                        :class="
                            position.get(item)?.includes(index) ? 'red' : ''
                        "
                        :key="key"
                    >
                        {{ key }} </span
                    >"
                    {{ idx === result.length - 1 ? '' : ',' }}
                </span>
                ]
            </p>
            <p v-else>匹配结果： {{ result }}</p>
        </div>
    </div>
</template>

<script>
import pinyinFuzzSearch, {pinyinFuzzySearchAdvance} from '@/pinyin_search';

export default {
    methods: {
        match(advance = false) {
            this.advance = advance;
            if (advance) {
                const list = this.list.replaceAll('，', ',').split(',');
                const result = pinyinFuzzySearchAdvance(this.word, list, {
                    sort: this.sort,
                    multiple: this.multiple,
                    separator: this.separator,
                });
                this.result = result.result;
                this.position = result.position;
            } else {
                const list = this.list.replaceAll('，', ',').split(',');
                this.result = pinyinFuzzSearch(this.word, list, {
                    sort: this.sort,
                    multiple: this.multiple,
                    separator: this.separator,
                });
            }
        },
    },
    data() {
        return {
            sort: 'AUTO',
            multiple: 'ANY',
            advance: true,
            separator: ',',
            word: 'bj',
            list: '北京市,天津市,河北省,山西省,内蒙古自治区,辽宁省,吉林省,黑龙江省,上海市,江苏省,浙江省,安徽省,福建省,江西省,山东省,河南省,湖北省,湖南省,广东省,广西壮族自治区,海南省,重庆市,四川省,贵州省,云南省,西藏自治区,陕西省,甘肃省,青海省,宁夏回族自治区,新疆维吾尔自治区',
            result: ['北京市'],
            position: new Map().set('北京市', [0, 1]),
        };
    },
};
</script>

<style>
.red {
    color: red;
}
</style>
