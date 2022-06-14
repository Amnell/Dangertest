# frozen_string_literal: true

is_release = github.branch_for_head.start_with?('release/')
is_hotfix = github.branch_for_head.start_with?('hotfix/')

changelog.filename = 'CHANGELOG.md'
# changelog.format = :keep_a_changelog
changelog.ignore_files = ['README.md', 'UPGRADING.md', /\.github.*$/]
changelog.placeholder_line = "* Your change here.\n"

################################################
# Welcome message
markdown [
  "Hey 👋 I'm Inga, the friendly bot watching over the mobile team 🤖",
  'Thanks a lot for your contribution!',
  '', '---', ''
]

need_fixes = []

################################################
# Make it more obvious that a PR is a work in progress and shouldn't be merged yet
warn('PR is classed as Work in Progress') if github.pr_title.include? '[WIP]'

# Warn when there is a big PR
warn('Big PR. Check twice to see if it is possible to break up the pull request into multiple.') if git.lines_of_code > 500 && !is_release

# Warn if the PR is a draft
warn('Draft PR. Some Workflows will not trigger.') if github.pr_draft?

# Ensure a clean commits history
if git.commits.any? { |c| c.message =~ /^Merge branch '#{github.branch_for_base}'/ }
  need_fixes << fail("Please rebase to get rid of the merge commits in this PR")
end

################################################
# Check for correct base branch
to_develop = github.branch_for_base == 'develop'
to_main = github.branch_for_base == 'main'
if is_release
  message('This is a Release PR')
  need_fixes << warn('Release branches should be merged into the `main` branch') unless to_main
elsif is_hotfix
  message('This is a Hotfix PR')
elsif !to_develop
  need_fixes << fail("Feature branches should start from and be merged into 'develop', " \
    "not #{github.branch_for_base}")
end

################################################
# Check `lock` files
package_changed = git.modified_files.include?('Package.resolved')
if package_changed
  need_fixes << warn("Package dependencies changed. Make sure to do regression testing.")
end

# ################################################
# # Check for a CHANGELOG entry
# declared_trivial = github.pr_title.include? '#trivial'
# has_changelog = git.modified_files.include?('CHANGELOG.md')
# changelog_msg = ''
# unless has_changelog || declared_trivial
#   repo_url = github.pr_json['head']['repo']['html_url']
#   pr_title = github.pr_title
#   pr_title += '.' unless pr_title.end_with?('.')
#   pr_number = github.pr_json['number']
#   pr_url = github.pr_json['html_url']
#   pr_author = github.pr_author
#   pr_author_url = "https://github.com/#{pr_author}"

#   need_fixes = fail("Please include a CHANGELOG entry to credit your work.  \nYou can find it at [CHANGELOG.md](#{repo_url}/blob/#{github.branch_for_head}/CHANGELOG.md).")

#   changelog_msg = <<-CHANGELOG_FORMAT.gsub(/^ *\|/, '')
#   |📝 We use the following format for CHANGELOG entries:
#   |```
#   |- #{pr_title} [@#{pr_author}](#{pr_author_url})
#   |```
#   CHANGELOG_FORMAT
#   # changelog_msg is printed during the "Encouragement message" section, see below
# end

################################################
# Check `lock` files

changelog_ok = changelog.check!

################################################
# Encouragement message
if need_fixes.empty? && changelog_ok == true
  markdown('Seems like everything is in order 👍 You did a good job here! 🤝')
else
  markdown('Once you fix those tiny nitpickings above, we should be good to go! 🙌')
  markdown('ℹ️ _I will update this comment as you add new commits_')
end
