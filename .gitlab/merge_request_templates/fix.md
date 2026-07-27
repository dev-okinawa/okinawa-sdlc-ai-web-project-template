### General Checklist

- [ ] I have rebased source branch **✓**
- [ ] I reviewed my MR by myself and did not find issues **✓**
- [ ] **Draft** removed from merge request title **✓**
- [ ] Branch name matching `{issue-code}-issue-summary` **✓**
- [ ] MR title matching `{ISSUE-CODE} Issue Summary` **✓**

### Fix Checklist

- [ ] Root cause is identified and described in the MR **✓**
- [ ] Fix addresses the cause, not only the symptom **✓**
- [ ] I added a regression test covering the bug **✓** (_if possible_)
- [ ] I checked related places for the same defect **✓**
- [ ] I have tested the fix and all looks good **✓**

### Reviewer Checklist

- [ ] **There is nothing to review**


- **Understanding of MR** (after a quick look):
    - [ ] very clear **✓**
    - [ ] approximately everything is clear
    - [ ] unclear **!** (_simplify code or add comments_)


- **Fix Quality**:
    - [ ] root cause eliminated **✓**
    - [ ] symptom patched, cause remains **!** (_fix or create a task_)


- **Tests**
    - [ ] regression test added **✓**
    - [ ] not required **✓**
    - [ ] need to add more tests **!!!** (_add or create a task_)
