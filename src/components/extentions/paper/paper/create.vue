<template>
  <div class="h-panel w-1200">
    <div class="h-panel-bar">
      <span class="h-panel-title">添加</span>
      <div class="h-panel-right">
        <Button color="primary" @click="create">添加</Button>
        <Button @click="$emit('close')" :text="true">取消</Button>
      </div>
    </div>
    <div class="h-panel-body">
      <Form mode="block" ref="form" :validOnChange="true" :showErrorTip="true" :rules="rules" :model="paper">
        <Row :space="10">
          <Cell :width="18">
            <FormItem label="标题" prop="title">
              <input type="text" v-model="paper.title" placeholder="试卷标题" />
            </FormItem>
          </Cell>

          <Cell :width="6">
            <FormItem label="分类" prop="category_id">
              <Select v-model="paper.category_id" :datas="categories" keyName="id" titleName="name" :filterable="true"></Select>
            </FormItem>
          </Cell>

          <Cell :width="6">
            <FormItem label="分数" prop="score">
              <input type="number" v-model="paper.score" placeholder="分数" />
            </FormItem>
          </Cell>

          <Cell :width="6">
            <FormItem label="及格分" prop="pass_score">
              <input type="number" v-model="paper.pass_score" placeholder="及格分" />
            </FormItem>
          </Cell>

          <Cell :width="6">
            <FormItem label="时间" prop="expired_minutes">
              <input type="number" v-model="paper.expired_minutes" min="0" placeholder="单位：分钟" />
            </FormItem>
          </Cell>
          <Cell :width="6">
            <FormItem label="可重复考试次数" prop="try_times">
              <input type="number" v-model="paper.try_times" min="0" placeholder="填写0意味着不限制" />
            </FormItem>
          </Cell>
        </Row>

        <Row :space="10">
          <Cell :width="24">
            <FormItem label="仅邀请" prop="enabled_invite">
              <h-switch v-model="paper.enabled_invite" :trueValue="1" :falseValue="0"></h-switch>
              <br />
              <warn text="只有在后台添加的用户才可以参与考试。该使用场景如：老师指定的一批学生科参与考试。"></warn>
            </FormItem>
          </Cell>
        </Row>

        <template v-if="paper.enabled_invite === 0">
          <Row :space="10">
            <Cell :width="12">
              <FormItem label="免费" prop="is_free">
                <h-switch v-model="paper.is_free" :trueValue="1" :falseValue="0"></h-switch>
                <br />
                <warn text="所有人都可以参与考试。"></warn>
              </FormItem>
            </Cell>
          </Row>

          <Row :space="10" v-if="paper.is_free === 0">
            <Cell :width="24">
              <FormItem label="会员可参与" prop="is_vip_free">
                <h-switch v-model="paper.is_vip_free" :trueValue="1" :falseValue="0"></h-switch>
                <br />
                <warn text="VIP用户可直接参与考试"></warn>
              </FormItem>
            </Cell>
            <Cell :width="12">
              <FormItem label="价格" prop="charge">
                <input type="number" v-model="paper.charge" />
                <warn text="价格大于0的话用户可以购买此试卷参与考试，价格为0的话则禁止购买"></warn>
              </FormItem>
            </Cell>
            <Cell :width="12">
              <FormItem label="购买指定课程可参与">
                <Select v-model="requireCourses" :datas="courses" :multiple="true" keyName="id" titleName="title" :filterable="true"></Select>
                <warn text="购买其中一门课程即可参与考试"></warn>
              </FormItem>
            </Cell>
          </Row>
        </template>
      </Form>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      paper: {
        category_id: null,
        title: null,
        thumb: null,
        pass_score: null,
        expired_minutes: null,
        is_random: 0,
        try_times: null,
        is_vip_free: 0,
        is_free: 0,
        charge: 0,
        random_rule: null,
        random_category_id: null,
        required_courses: null,
        enabled_invite: 0
      },
      requireCourses: [],
      rules: {
        required: [
          'category_id',
          'title',
          'score',
          'pass_score',
          'expired_minutes',
          'is_random',
          'try_times',
          'is_vip_free',
          'is_free',
          'enabled_invite'
        ]
      },
      createParams: {},
      categories: [],
      questionCategories: [],
      courses: []
    };
  },
  mounted() {
    this.init();
  },
  methods: {
    init() {
      R.Extentions.paper.Paper.Create().then(res => {
        this.categories = res.data.categories;
        this.questionCategories = res.data.questionCategories;
        this.courses = res.data.courses;
      });
    },
    create() {
      let validResult = this.$refs.form.valid();
      if (validResult.result) {
        let data = this.paper;
        data.required_courses = this.requireCourses.join(',');
        R.Extentions.paper.Paper.Store(data).then(() => {
          HeyUI.$Message.success('添加成功');
          this.$emit('success');
        });
      }
    }
  }
};
</script>
