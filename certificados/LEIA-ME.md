# Sincronização automática: Certificados → Log CPD/CE/CEU/ACG

## O que isso faz
Você salva o PDF do certificado numa pasta do Google Drive (a mesma ação que
já faz hoje, só que numa pasta em vez de direto no LinkedIn). Um script roda
sozinho, uma vez por dia, sem você precisar abrir nada, e atualiza sua
planilha de log automaticamente.

## Passo a passo da instalação (uma vez só, leva ~10 min)

### 1. Crie a pasta e a planilha
- Crie uma pasta no Google Drive chamada, por exemplo, `Certificados CPD`.
- Crie (ou abra) sua planilha de log no Google Sheets. Se ainda não existe,
  pode criar uma em branco — o script cria os cabeçalhos sozinho na primeira
  execução.
- Dentro da planilha, crie (ou renomeie) uma aba chamada exatamente `Log CPD`
  (ou ajuste o nome no script, explico abaixo).

### 2. Pegue os IDs
- Abra a pasta de certificados no navegador. O ID é o trecho da URL depois de
  `folders/`: `drive.google.com/drive/folders/`**`ESTE_TRECHO_AQUI`**
- Abra a planilha. O ID é o trecho da URL depois de `/d/`:
  `docs.google.com/spreadsheets/d/`**`ESTE_TRECHO_AQUI`**`/edit`

### 3. Cole o script
- Na planilha, vá em **Extensões → Apps Script**.
- Apague o conteúdo padrão e cole todo o conteúdo do arquivo `Sync.gs`.
- No topo do script, substitua:
  - `COLE_AQUI_O_ID_DA_PASTA_DO_DRIVE` pelo ID da pasta (passo 2)
  - `COLE_AQUI_O_ID_DA_PLANILHA_DE_LOG` pelo ID da planilha (passo 2)
  - `Log CPD` pelo nome real da sua aba, se for diferente

### 4. Ative a API do Drive avançada (necessária para o OCR)
- No editor do Apps Script, clique no ícone **+** ao lado de "Serviços"
  (barra lateral esquerda).
- Selecione **Drive API** (a versão "avançada") e clique em Adicionar.

### 5. Ligue a automação (só uma vez)
- No editor, selecione a função `criarGatilhoDiario` no menu suspenso de
  funções (topo) e clique em **Executar**.
- Na primeira vez, o Google vai pedir autorização — aceite (é o script
  acessando sua própria pasta e planilha, nada externo).
- Pronto. A partir daqui, `syncCertificates` roda sozinha todo dia às 6h.

## Uso do dia a dia
Você não faz mais nada. Só continua salvando o PDF do certificado na pasta
`Certificados CPD` sempre que concluir um curso. No dia seguinte (ou quando
quiser forçar manualmente, rodando `syncCertificates` direto no editor), a
linha aparece no log.

## Sobre a coluna STATUS
- **OK** → todos os campos principais foram identificados com confiança.
- **REVISAR** → algum campo (data, carga horária, categoria ou instituição)
  não foi reconhecido no texto do PDF. A linha existe, o link do certificado
  está lá, só falta você completar manualmente o campo indicado na coluna
  Observações. Isso é esperado — certificados têm formatos muito diferentes
  entre emissores, e o script prioriza nunca perder um registro em vez de
  tentar adivinhar um dado errado.

## Se quiser desativar o e-mail de resumo diário
No arquivo `Sync.gs`, comente (coloque `//` na frente) as linhas dentro da
função `notifyUser`, ou simplesmente não chame essa função.

## Limitações importantes (honestidade antes de tudo)
- O script só processa PDFs. Certificados em imagem (JPG/PNG) direto na
  pasta não são lidos — teria que ser adaptado se for o seu caso.
- A extração de curso/instituição é heurística (baseada em padrões de texto
  comuns). Emissores muito informais ou com PDFs escaneados de baixa
  qualidade vão cair mais em "REVISAR" — é esperado, não é bug.
- Isso depende 100% de você salvar o PDF na pasta certa. Se algum certificado
  ficar só no LinkedIn e nunca for salvo na pasta, ele não entra no log —
  não existe ponte automática vinda do LinkedIn (conforme expliquei antes).
