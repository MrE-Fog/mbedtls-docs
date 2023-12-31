# Guidelines for Contributors

## Introduction

This document describes the process of having your pull request (PR) reviewed. For a wider overview of the contribution process, see [CONTRIBUTING.md](https://github.com/Mbed-TLS/mbedtls/blob/development/CONTRIBUTING.md).

## Goals of the review process

The review process is there to ensure the quality (security, maintainability, etc.) of the code base. In order to be merged, a PR needs to pass the CI and be approved by at least two trusted reviewers; a trusted gatekeeper will then check everything's in order and press the merge button.

See the [guideline for reviewers](<review_guidelines.md>) for a list of things reviewers will check.

## General considerations

As a project, we find reviewer bandwidth to be our most common bottleneck. As a consequence, the review process should be optimised for reviewers. We recommend the following flow:

1. Make a (draft) pull request.
2. Wait for the CI results.
3. If there are failures, fix them and go back to 2. (Ask for help if needed.)
4. Move the draft to a pull request. If you have label-setting permissions, remove "needs-ci" and "needs-work" and add "needs-reviewer" and "needs-review". If you don't have label-setting permissions and someone hasn't already set the labels, post a comment saying the pull request is ready for review.

Reviewers will generally assign themselves on a PR when there are available to review it, or when they commit to reviewing it later. You should not request specific reviewers on a PR unless this was previously agreed with them.

Large contributions (say, more than 500 lines) should be discussed in advance on the mailing list, and as much as possible broken down into a series of smaller PRs.

Please note that we have a pretty high review bar. Reviewers should not approve a PR unless they fully understand it and are convinced it's correct, secure and maintainable. If they're not confident about the code, they don't need to find an actual bug in order to reject it; the burden of proof is on the submitter.

## Use of GitHub

We use GitHub reviewing facilities; these have a few limitations which impact our review process (see below).

## Reworking your PR in response to review comments

* Please address feedback by adding commits to your PR, not by rewriting existing commits. Do so using atomic commits with descriptive messages. Avoid commits with titles like "address review feedback" that contains several unrelated changes.
* Once you have addressed one point of feedback, please leave the GitHub discussion unresolved: the person who raised the point should mark it as resolved if they are satisfied with the resolution. This allows reviewers to easily track if all of their feedback has been addressed.
  * If you want a way to mark which points you think you addressed, we tend to do that by reacting with the `:rocket:` emoji to the original comment. (We also use the thumbs up emoji as a way of saying we agree with the comment and intend to address it.) Note that this is purely optional.
* Please avoid force-pushing once review has started, except in the circumstances listed below; this tends to disrupt incremental review and is not supported well enough by GitHub's reviewing tools. Be particularly careful about force-pushing several times in a row: GitHub sometimes merges the events in the PR's timeline, showing only details of the last force-push, making it impossible to find again which commit was last reviewed. Exceptions:
  * It's OK to force-push as much as you want before review has started (for example, when your PR is still a draft). However, please avoid squashing away useful insights about the history of the code.
  * As a general rule, if you force-push after receiving some review, check with the reviewers if it is OK with them, and make a note in a GitHub comment with the hash of the commit that was last reviewed.
  * It is generally OK to force-push changes that haven't been reviewed yet as long as (1) you don't alter commits that have been reviewed yet, and (2) you're confident nobody is reviewing at the same time (for example, because you told the reviewers to wait before reviewing further).
  * The most common reason for force-pushing is when conflicts with the base branch have appeared. We usually resolve these by rebasing. (Though, for complex PRs, merging the base branch into the PR can be considered; this should be discussed with reviewers too.)
  * Sometimes force-pushing is necessary to fix a commit message. We usually don't do it for simple typo fixes but only when a sign-off is missing or the message is misleading or missing an important piece for information. As usual, if in doubt check with the reviewers.
  * It is generally acceptable to force-push simple fixes (such as typos) to the last commit (but only that one) in the PR shortly after it was pushed.
  * Once a PR is approved, some people like to have a zero-diff force-push to clean up the history (like, fixing typos or syntax errors in the commit they were introduced, etc.). This is not a general rule and should be discussed with the reviewers. We like to keep a history that reflects the evolution of the code; in particular we don't generally squash the entire PR to a single commit before merging.
* If you have appropriate permissions on GitHub, when you think you have addressed feedback and the PR is ready for the next round of reviews, please update labels (remove "needs-work" and add "needs-review") and re-request review from existing reviewers. Otherwise, please leave a comment.

## Creating backports

Some changes need to be backported to the Long-Term Support (LTS) branches: security fixes, bug fixes, and some testing improvements. In that case, as a general rule we wait until the backport is ready and approved before merging the main PR.

A backport is a separate PR that, instead of targeting `development` as usual, targets an LTS branch. That secondary PR should have the same title as the main one with "Backport x.yy: " prepended, where x.yy is the name of LTS branch (for example 2.28). The description should start with "This is the x.yy backport of #zzzz", where zzzz is the number of the original PR, and then mention if the backport is trivial, or if there were conflicts that had to be resolved, or important differences with the main branch (for example: "only contextual conflicts", or "had to add/remove XXX because feature YYY is present only on one side", or "skipped commits U and V because they were irrelevant").

Trivial backports can be created by making a new branch base on the LTS branch, then running `git cherry-pick development..original_branch`. Regardless of how you create the backports, please try to follow the same commit structure as the original branch (possibly with some commits skipped), unless the backport is too different for it to make sense.


