<template>
    <div class="main-view">
        <div class="title-view">
            <div class="vote-view">
                <span class="agree-icon-view"><span class="agree-icon" :class="{'agree-icon-unavail' : hasVote}" @click="addVote"></span></span>
                <p>{{votes}}</p>
            </div>
            <p class="title-content">
                {{title}}({{domain}})
            </p>
            <p> by {{lastCommentUser}} {{lastCommentTime}} | {{commentsNum}} comments</p>
        </div>
        <div>
            <el-row>
                <el-col :span="8">
                    <el-input v-model="commentsInput" type="textarea" placeholder="comment"></el-input>
                </el-col>
            </el-row>
            <el-row>
                <el-button round size="mini" class="comment-btn" @click="addComment">add comment</el-button>
            </el-row>
        </div>
        <br>
        <br>
        <div class="comment-lists-view">
            <div class="comment-item" v-for="(commentItem, index) in commentList" :key="index">
                <div class="vote-view">
                    <span class="agree-icon-view-comment"><span class="agree-icon-comment" :class="{'agree-icon-comment-unavail' : commentItem.hasVote}" @click="addVoteUserComment" :data-index="index"></span></span>
                    <p>{{commentItem.votes}}</p>
                </div>
                <p class="comment-title-info">
                    {{commentItem.user}} {{commentItem.createTime}}
                    <span class="collapse-icon" :data-index="index" @click.stop="toggleComment">[{{commentItem.hideComment ? '+' : '-'}}]</span>
                </p>
                <div class="comment-content" :class="{'comment-hide' : commentItem.hideComment}">
                    <p :class="{'comment-hide' : commentItem.hideComment}">{{commentItem.content}}</p>
                    <p class="comment-reply" :data-index="index" @click="showCommentReply">reply</p>
                    <div class="comment-detail-list-view">
                        <div class="comment-detail-list-item" v-for="(commentDetailItem, idx) in commentItem.commentDetailList" :key="idx">
                            <div class="vote-view">
                                <span class="agree-icon-view-comment"><span class="agree-icon-comment" :class="{'agree-icon-comment-unavail' : commentDetailItem.hasVote}" @click="addVoteComment" :data-index="index" :data-idx="idx"></span></span>
                                <p>{{commentDetailItem.votes}}</p>
                            </div>
                            <div>
                                <p class="comment-title-info">
                                    {{commentDetailItem.user}} {{commentDetailItem.createTime}}
                                    <span :data-index="index" :data-idx="idx" class="collapse-icon" @click.stop="toggleCommentDetail">[{{commentDetailItem.hideCommentDetail ? '+' : '-'}}]</span>
                                </p>
                                <div class="comment-content" :class="{'comment-hide' : commentDetailItem.hideCommentDetail}">
                                    {{commentDetailItem.content}}
                                    <p class="comment-reply" :data-index="index" :data-idx="idx" @click="showCommentReply">reply</p>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div :class="{'comment-reply-input-view-show': showComment}" class="comment-reply-input-view">
            <el-row>
                <el-input v-model="commentsUserInput" type="textarea" :placeholder="commentPlaceHolder"></el-input>
            </el-row>
            <el-row>
                <el-button round size="mini" class="comment-btn" @click="addUserComment">add comment</el-button>
            </el-row>
        </div>
    </div>
</template>

<script>

const moment = require('moment');
let momentCount = 0;

function getMoment() {
    return moment().subtract(`${momentCount++}`, "hours").format('YYYY-MM-DD HH:mm:ss');
}

export default {
    name: 'Home',
    components: {

    },
    data() {
        return {

            title: 'how to detact mobile devices',
            domain: 'www.stackoverflow.com',
            votes: 988,
            author: 'jack',
            createTime: getMoment(),
            commentsNum: 0,
            lastCommentUser: 'Depp',
            lastCommentTime: getMoment(),
            commentsInput: '',
            hasVote: false,

            commentsUserInput: '',
            showComment: false,
            commentPlaceHolder: 'comment',

            commentList: [{
                user: 'Chou',
                createTime: getMoment(),
                content: 'jQuery does not, and cannot do everything. It is provides cross-browser DOM traversal and manipulation, simple animation and ajax between browsers, and creates a skeleton framework for plugins to build upon. Please be aware of jQuerys limitations before asking ',
                votes: 5,
                commentDetailList: [
                    {
                        user: 'Lee',
                        createTime: getMoment(),
                        content: 'Is there a solid way to detect whether or not a user is using a mobile device in jQuery? Something similar to the CSS @media attribute? I would like to run a different script if the browser is on a handheld device.',
                        votes: 0,
                        answerTo: 'Chou'
                    }, {
                        user: 'Chen',
                        createTime: getMoment(),
                        content: 'User agents are constantly moving targets, everyone reading this post should be very wary of user agent sniffing',
                        votes: 0,
                        answerTo: 'Chou'
                    }
                ]
            }, {
                user: 'Chou',
                createTime: getMoment(),
                content: 'jQuery does not, and cannot do everything. It is provides cross-browser DOM traversal and manipulation, simple animation and ajax between browsers, and creates a skeleton framework for plugins to build upon. Please be aware of jQuerys limitations before asking ',
                votes: 5,
                commentDetailList: [
                    {
                        user: 'Lee',
                        createTime: getMoment(),
                        content: 'Is there a solid way to detect whether or not a user is using a mobile device in jQuery? Something similar to the CSS @media attribute? I would like to run a different script if the browser is on a handheld device.',
                        votes: 0,
                        answerTo: 'Chou'
                    }, {
                        user: 'Chen',
                        createTime: getMoment(),
                        content: 'User agents are constantly moving targets, everyone reading this post should be very wary of user agent sniffing',
                        votes: 0,
                        answerTo: 'Chou'
                    }
                ]
            }]

        };
    },
    beforeMount() {
        this.hasVote = localStorage.getItem('hasVote') === 'true' ? true : false;
        this.votes = this.hasVote ? (this.votes + 1) : this.votes;
        let commentNum = 0;
        this.commentList.forEach((item) => {
            commentNum++;
            if (item.commentDetailList) {
                commentNum += item.commentDetailList.length;
            }
        })
        this.commentsNum = commentNum;
    },
    methods: {
        toggleComment(e) {
            const el = e.currentTarget;
            const { index } = el.dataset;
            const toggle = !this.commentList[index].hideComment;
            this.$set(this.commentList[index], 'hideComment', toggle);
        },
        toggleCommentDetail(e) {
            const el = e.currentTarget;
            const { index, idx } = el.dataset;
            const toggle = !this.commentList[index].commentDetailList[idx].hideCommentDetail;
            this.$set(this.commentList[index].commentDetailList[idx], 'hideCommentDetail', toggle);
        },
        getUser() {
            let user = localStorage.getItem('user');
            if (!user) {
                user = this.getRandomStr(6);
                localStorage.setItem('user', user);
            }
            return user;
        },
        addComment(e) {
            if (!this.commentsInput) return;
            const user = this.getUser();
            this.commentList.unshift({
                user,
                createTime: moment().format('YYYY-MM-DD HH:mm:ss'),
                content: this.commentsInput,
                votes: 0,
                answerTo: 'Chou'
            });
            this.commentsInput = '';
            this.commentsNum += 1;
            this.showComment = false;
            this.$message('success');
        },
        getRandomStr(len) {
            len = len || 32;
            const base = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789';
            const baseLen = base.length;
            let str = '';
            for (let i = 0; i < len; i++) {
                str += base.charAt(Math.floor(Math.random() * baseLen));
            }
            return str;
        },
        addVote() {
            this.hasVote = !this.hasVote;
            this.votes = this.hasVote ? this.votes + 1 : this.votes - 1;
            localStorage.setItem('hasVote', this.hasVote);
            this.$message('success');
        },
        showCommentReply(e) {
            const el = e.currentTarget;
            const { index, idx } = el.dataset;
            this.showComment = true;
            this.commentIndex = index;
            this.commentIdx = idx;
            let answerUser = '';
            if (typeof idx === 'undefined') {
                answerUser = this.commentList[index].user;
            } else {
                answerUser = this.commentList[index].commentDetailList[idx].user;
            }
            this.commentPlaceHolder = `@${answerUser}`;
        },
        addUserComment(e) {
            const {commentIndex, commentIdx} = this;
            let answerUser = '';
            if (!this.commentsUserInput) return;
            if (typeof commentIdx === 'undefined') {
                answerUser = this.commentList[commentIndex].user;
            } else {
                answerUser = this.commentList[commentIndex].commentDetailList[commentIdx].user;
            }
            this.commentList[commentIndex].commentDetailList = this.commentList[commentIndex].commentDetailList || [];
            this.commentList[commentIndex].commentDetailList.unshift({
                user: this.getUser(),
                createTime: moment().format('YYYY-MM-DD HH:mm:ss'),
                content: `@${answerUser}: ${this.commentsUserInput}`,
                votes: 0,
                answerTo: answerUser
            });
            this.commentsUserInput = '';
            this.commentsNum += 1;
            this.showComment = false;
            this.$message('success');
        },
        addVoteComment(e) {
            const el = e.currentTarget;
            const { index, idx } = el.dataset;
            const item = this.commentList[index].commentDetailList[idx];
            const { votes, hasVote } = item;
            if (hasVote) {
                this.$set(item, 'votes', votes - 1);
                this.$set(item, 'hasVote', false);
            } else {
                this.$set(item, 'votes', votes + 1);
                this.$set(item, 'hasVote', true);
            }
        },
        addVoteUserComment(e) {
            const el = e.currentTarget;
            const { index } = el.dataset;
            const item = this.commentList[index];
            const { votes, hasVote } = item;
            if (hasVote) {
                this.$set(item, 'votes', votes - 1);
                this.$set(item, 'hasVote', false);
            } else {
                this.$set(item, 'votes', votes + 1);
                this.$set(item, 'hasVote', true);
            }
        }
    }
}
</script>

<style>
    .comment-btn {
        margin-top: 10px;
    }

    .main-view {
        font-size: 14px;
        width: 700px;
        margin: 30px auto 0;
    }

    .title-view {
        font-size: 14px;
    }

    .comment-detail-list-view {
        margin-left: 50px;
    }

    .comment-detail-list-item {
        margin-top: 5px;
    }

    .comment-reply {
        text-decoration: underline;
        cursor: pointer;
    }

    .title-content {
        font: 18px;
        color: black;
    }

    .comment-title-info {
        color: #3c3c3c;
        font-size: 12px;
    }

    .agree-icon-view, .agree-icon-view-comment {
        display: inline-block;
        height: 12px;
        overflow: hidden;
        width: 24px;
    }
    
    .agree-icon-view-comment {
        height: 10px;
        width: 20px;
    }

    .agree-icon, .agree-icon-comment {
        color: #9c9c9c;
        display: inline-block;
        width: 0;
        height: 0;
        border: 12px solid transparent;
        border-bottom-color: #3c3c3c;
        cursor: pointer;
        margin-top: -12px;
    }

    .agree-icon-comment {
        margin-top: -10px;
        border-width: 10px;
    }
    
    .collapse-icon {
        cursor: pointer;
    }

    .comment-content {
        height: auto;
        overflow: hidden;
        transition: all .5s;
        /* max-height: 300px; */
    }

    .comment-hide {
        height: 20px;
        text-overflow: ellipsis;
        white-space: nowrap;
        overflow: hidden;
    }

    .agree-icon-unavail, .agree-icon-comment-unavail {
        border-bottom-color: #00e200;
    }

    .comment-reply-input-view {
        position: fixed;
        height: 300px;
        width: 600px;
        left: 50%;
        margin-left: -300px;
        bottom: -300px;
        overflow: hidden;
        transition: all .5s;
        background: #ffffff;
        z-index: 1;
    }

    .comment-reply-input-view-show {
        bottom: 0;
    }

    .vote-view {
        float: left;
        margin-right: 5px;
        margin-top: -3px;
        text-align: center;
    }

    .comment-item {
        margin-top: 10px;
    }

</style>
