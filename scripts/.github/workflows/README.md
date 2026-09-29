
🤖🌐 IA & Política Externa — Monitor de Notícias
Dashboard estático, atualizado automaticamente todas as manhãs, com notícias internacionais sobre inteligência artificial e política externa, com recorte de interesse para o Brasil. As matérias são coletadas via RSS de grandes veículos internacionais, classificadas por tema e exibidas em um painel interativo com manchetes, resumos e link de acesso.

Baseado no projeto Embassy Daily News, adaptado do recorte "imprensa indiana" para o tema IA e política externa.

Temas de interesse
Tema	Descrição
🇧🇷 Brasil	Brasil em IA e política externa: governo, diplomacia, empresas e iniciativas nacionais
⚖️ IA: Governança e Regulação	Regulação, segurança, ética e políticas públicas de IA
🤖 IA: Modelos e Pesquisa	Modelos, pesquisa e avanços técnicos em IA
💼 IA: Indústria e Investimentos	Empresas, investimentos, chips e infraestrutura de IA
🛡️ IA: Segurança e Defesa	Uso militar/de defesa de IA, guerra cibernética, segurança nacional
🌐 Diplomacia e Multilateralismo	Diplomacia, relações bilaterais e organismos multilaterais
🌍 Geopolítica e Grandes Potências	Disputas e alinhamentos entre grandes potências, ordem mundial
⚔️ Conflitos e Segurança Internacional	Guerras, conflitos armados e crises de segurança internacional
📦 Comércio, Sanções e Exportação	Comércio internacional, tarifas, sanções e controles de exportação
📝 Opiniões & Análises	Artigos de opinião, análise e reflexão estratégica
Como funciona
 GitHub Actions (cron diário, 08h Brasília)
        │
        ├── scripts/build.py
        │     ├── busca os feeds RSS/Atom (lista em FEEDS)
        │     ├── classifica cada matéria por palavras-chave + dica de seção
        │     ├── filtra para os últimos N dias e remove duplicatas
        │     └── gera public/index.html + public/data.json
        │
        └── publica em GitHub Pages
Sem dependências externas: o build.py usa apenas a biblioteca padrão do Python (parser de RSS/Atom próprio). Não precisa de pip install.

Gratuito: GitHub Actions + GitHub Pages, sem servidor.

As manchetes e resumos são mantidos no idioma original (inglês); a interface é em português.

Ativando o GitHub Pages (uma única vez)
No repositório: Settings → Pages → Build and deployment → Source: GitHub Actions.
O workflow roda diariamente às 11:00 UTC (08:00 Brasília). Para rodar na hora, vá em Actions → "Atualização diária do dashboard" → Run workflow.
A página ficará disponível em https://<seu-usuário>.github.io/<repositório>/.
Ranking e curadoria por IA
As matérias são ordenadas por um ranking heurístico de relevância (determinístico, sem custo): peso por tema (Brasil ≫ governança de IA e diplomacia > demais), veículo de grande circulação, recência e cruzamento de temas.

Opcionalmente, uma camada de IA (Gemini) gera os Destaques do dia, resumos de 1 frase em português e as 🧭 Narrativas do dia — uma síntese analítica no topo da página Início, com o quadro geral do dia e as principais narrativas de IA e política externa (os Destaques vêm logo abaixo). Cada narrativa passa por um aprofundamento: a IA identifica o tema, faz uma pesquisa adicional específica no Google News e entrega um briefing estritamente factual — síntese e fatos-chave em bullets (nomes, números, datas), com fontes. Sem análise: a leitura é da diplomata. É totalmente opcional e com fallback automático: sem a chave (ou se a cota/rede falhar), o painel funciona 100% com o ranking heurístico (e o bloco de narrativas simplesmente não aparece).

Ativar o Gemini (gratuito):

Crie uma API key em https://aistudio.google.com/apikey (free tier).
No repositório: Settings → Secrets and variables → Actions → New repository secret, nome GEMINI_API_KEY, valor = sua chave.
Rode o workflow. Modelo padrão: gemini-3.6-flash (ajustável via variável GEMINI_MODEL).
Personalização
Fontes: edite a lista FEEDS em scripts/build.py.
Palavras-chave dos temas: edite o dicionário THEMES no mesmo arquivo.
Veículos aceitos em buscas agregadas do Google News: edite TRUSTED_OUTLETS; os de maior peso (estrelinha ★) estão em PRIORITY_OUTLETS.
Horário: ajuste o cron em .github/workflows/daily.yml (está em UTC).
Janela de notícias: por padrão mostra apenas as últimas 24 horas (janela rolante; 48h às segundas). MAX_AGE_DAYS força uma janela maior (uso em testes).
Marca/cabeçalho: o logo é um SVG no TEMPLATE de scripts/build.py (procure por logo-svg); título e subtítulo ficam logo abaixo.
Variáveis de ambiente
Variável	Padrão	Descrição
OUTPUT_DIR	public	Diretório de saída
MAX_AGE_DAYS	—	Janela rolante em dias (testes); sem ela, valem as últimas 24 horas
FEEDS_OVERRIDE	—	JSON de feeds para teste local
MIN_ARTICLES	30 (0 em teste)	Piso de matérias: abaixo disso o build aborta sem publicar, preservando a edição anterior
HISTORY_DIR	history	Pasta versionada com os snapshots diários (menu Hoje/Ontem/Anteontem)
GEMINI_API_KEY	—	Ativa a camada de IA (ranqueamento/destaques/resumos)
GEMINI_MODEL	gemini-3.6-flash	Modelo da camada de IA (pontuação/curadoria)
GEMINI_MODEL_NARRATIVES	gemini-3.6-flash	Modelo das Narrativas do dia (raciocínio profundo). Ajuste se o modelo indicado não estiver disponível na sua conta
ANTHROPIC_API_KEY	—	Opcional (API paga). Com esta secret, as Narrativas usam um modelo de ponta da Anthropic. Prioridade sobre a OpenAI se ambas existirem
OPENAI_API_KEY	—	Opcional (API paga). Com esta secret, as Narrativas usam um modelo de ponta da OpenAI. Sem nenhuma das duas, seguem no Gemini (grátis)
Histórico de 3 dias (Hoje / Ontem / Anteontem)
O cabeçalho traz um menuzinho com as edições dos últimos 3 dias. Cada dia é uma página estática autocontida (index.html = hoje; h-AAAA-MM-DD.html = dias anteriores) e o menu são apenas links entre elas. A cada execução o build:

salva o snapshot do dia em history/data-AAAA-MM-DD.json (versionado);
mantém só os 3 snapshots mais recentes (poda os demais);
renderiza uma página por dia e o menu correspondente.
O passo "Persistir histórico" do workflow faz commit da pasta history/ para que os snapshots sobrevivam entre execuções (exige permissions: contents: write).

Rodando localmente
# Gera o dashboard a partir dos feeds reais (requer internet)
python3 scripts/build.py
# Saída em public/index.html

# Teste rápido com um feed de exemplo (offline)
FEEDS_OVERRIDE='[{"name":"Sample","url":"tests/sample_feed.xml","themes":[]}]' \
MAX_AGE_DAYS=3650 python3 scripts/build.py
