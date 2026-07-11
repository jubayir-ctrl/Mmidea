name: Surveillance stock PortaSplit

on:
  schedule:
    # ~toutes les 15 min. GitHub : minimum 5 min, horaires non garantis.
    - cron: '7,22,37,52 * * * *'
  workflow_dispatch:
    inputs:
      test:
        description: 'Test Telegram ? (choisir "oui" pour recevoir un message de test)'
        type: choice
        options:
          - 'non'
          - 'oui'
        default: 'non'

permissions:
  contents: write

concurrency:
  group: stock-check
  cancel-in-progress: false

jobs:
  check:
    runs-on: ubuntu-latest
    timeout-minutes: 10
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: '3.12'
      - run: pip install requests
      - name: Verifier les stocks
        env:
          TELEGRAM_TOKEN: ${{ secrets.TELEGRAM_TOKEN }}
          TELEGRAM_CHAT_ID: ${{ secrets.TELEGRAM_CHAT_ID }}
          TEST_PING: "${{ github.event.inputs.test == 'oui' && '1' || '' }}"
        run: python check_stock.py
      - name: Sauvegarder l'etat
        run: |
          if [[ -n "$(git status --porcelain state.json)" ]]; then
            git config user.name "stock-bot"
            git config user.email "stock-bot@users.noreply.github.com"
            git add state.json
            git commit -m "maj etat stock [skip ci]"
            git push
          fi
