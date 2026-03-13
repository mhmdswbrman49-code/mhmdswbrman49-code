name: Chess

on:
  issues:
    types: [opened]

jobs:
  move:
    name: Play Chess
    runs-on: ubuntu-latest
    if: startsWith(github.event.issue.title, 'Chess:')

    steps:
      - uses: actions/checkout@v6

      - name: Setting up Python
        uses: actions/setup-python@v4
        with:
          python-version: "3.10"
          architecture: "x64"

      # ✅ Stockfish أولاً قبل أي شيء
      - name: Install stockfish
        run: |
          sudo apt-get update
          sudo apt-get install -y stockfish

      # ✅ ثم تثبيت المتطلبات وتشغيل اللعبة
      - name: Play chess
        env:
          ISSUE_NUMBER: ${{ github.event.issue.number }}
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          REPOSITORY_OWNER: ${{ github.repository_owner }}
        run: |
          pip install -r requirements.txt
          python main.py

      # ✅ أخيراً الحفظ والرفع
      - name: Commit and push changes
        env:
          ISSUE_TITLE: ${{ github.event.issue.title }}
          ISSUE_AUTHOR: ${{ github.event.issue.user.login }}
        run: |
          git config --global user.name "github-actions[bot]"
          git config --global user.email "41898282+github-actions[bot]@users.noreply.github.com"
          git add .
          git commit -m "${ISSUE_TITLE} by ${ISSUE_AUTHOR}"
          git push
