# OncoCusto

**Custos de pacientes com câncer pela tabela SIGTAP, na perspectiva do SUS.**

O OncoCusto transforma o registro assistencial (procedimentos, internações e medicamentos do prontuário) no **valor que o SUS paga pela tabela SIGTAP** e no preço dos medicamentos pelo **Banco de Preços em Saúde (BPS)**. O resultado sai numa planilha .xlsx por projeto, com avisos de conferência e os parâmetros usados.

É uma página estática, publicada no GitHub Pages. Não precisa instalar nada nem criar conta.

> O resultado é o valor de tabela (repasse federal), não o custo de produção do hospital.

> **Limitação.** O OncoCusto calcula o valor de tabela do SIGTAP (APAC, BPA e AIH) e o preço de medicamentos retirados fora de procedimentos, pelo BPS. Os antineoplásicos de quimioterapia estão dentro do valor da APAC. Medicamentos de compra centralizada pelo Ministério da Saúde e os do rol de altíssimo custo (Portaria Conjunta SCTIE/SAES/MS nº 2/2026) não são incluídos.

---

## Privacidade: nenhum dado de paciente sai do computador

- **Sem servidor, banco de dados ou login.** O site é só HTML, CSS e JavaScript; o GitHub entrega os arquivos e mais nada.
- **O cálculo acontece no navegador.** Busca de códigos, valores, preços, avisos e montagem da planilha são feitos localmente.
- **Nada é enviado.** O site não tem formulário nem chamada que mande dados para fora. As únicas conexões são downloads:

| Conexão | Para quê | Leva dados de pacientes? |
| --- | --- | --- |
| GitHub Pages (este repositório) | Página e bases da pasta `dados/` | Não |
| `cdnjs.cloudflare.com` | Biblioteca SheetJS 0.18.5 (ler e gravar .xlsx) | Não |
| `fonts.googleapis.com` / `fonts.gstatic.com` | Fonte IBM Plex | Não |

- **Onde os dados ficam:** no rascunho do navegador (`localStorage`, chave `oncocusto_rascunho_v1`), só naquele computador e perfil, até o usuário clicar em **Excluir** na tela *Continuar rascunho*; e na planilha .xlsx baixada.
- **Este repositório não contém dados de pacientes**, só código e bases públicas. Não suba planilhas exportadas para cá.

Para conferir: abra as ferramentas do desenvolvedor (F12) › aba *Rede* e registre algo. Só aparecem os downloads acima.

---

## Como usar

1. **Projeto:** identificação do estudo, usuário da sessão e decisões metodológicas (competência do SIGTAP, base do BPS, mínimo de compras, habilitação 17.17/17.18 etc.).
2. **Internações:** uma linha por AIH, com entrada e alta. As datas definem o local do ato (ambulatorial ou hospitalar).
3. **Procedimentos:** exames, consultas, cirurgias, quimioterapia, radioterapia, diárias e OPM, com conferência de OPM contra a cirurgia e as regras de custeio (APAC mensal, secundários, radioterapia, AIH sequenciais, incremento de 20%, situações). Códigos sem correspondência: 00.00.00.000-0 (procedimento) e 000000 (medicamento).
4. **Medicamentos:** retiradas fora de procedimentos, com a mediana do BPS (município → UF → região → Brasil; mínimo de 3 compras, plano B de 1 compra marcado como "base fraca").
5. **Revisão:** todas as entradas, filtros e avisos pendentes.
6. **Exportar:** planilha completa ou **sem prontuário** (para compartilhar). Células vazias saem como "Não informado"/"Não se aplica" (texto), 0 (número) ou 0000-00-00 (data); datas e horas de criação e exportação seguem o horário de Brasília (DD-MM-AAAA HH:MM:SS).
7. **Ajuda:** conferência no SIGTAP, o que é o id da planilha, limitações e contato.

A planilha exportada tem as abas Procedimentos, Internações, Medicamentos, Resumo, Avisos, Limitações, Parâmetros e Dicionário (significado, tipo, origem e regra de cada variável de cada aba), e pode ser reaberta no site para continuar o projeto em outro computador.

O passo a passo completo está no **Manual de apresentação e uso do site**; as regras de cálculo, no **Manual de regras de custeio do app**.

---

## Estrutura do repositório

```
index.html            página única: interface, regras e exportação
favicon.svg           ícone do site
apple-touch-icon.png  ícone para iPhone/iPad
dados/
  sigtap_procedimentos_MM_AAAA.json    procedimentos e valores por competência
  sigtap_compatibilidade_MM_AAAA.json  compatibilidades vigentes na competência
  bps_mediana_MM_AAAA.json             medianas do BPS por CATMAT, unidade e nível
  catmat_item_MM_AAAA.json             medicamentos (CATMAT) e unidades
  ibge_municipio_2025.json             municípios, UF e região (DTB/IBGE)
  situacao.json                        siglas de situação e efeito no custo
  meta_MM_AAAA.json                    período, data de geração e contagens
```

Fontes das bases: [SIGTAP/DATASUS](http://sigtap.datasus.gov.br), [Banco de Preços em Saúde](https://bps.saude.gov.br), [IBGE](https://www.ibge.gov.br).

## Autoria

Idealização, propriedade e manutenção: [Isabela Santos](https://github.com/isabelagsantos).

## Aviso de novidades

No fim da página aparece um quadro com o que mudou na versão. Ele some sozinho 1 mês depois da data `publicadoEm`. A cada publicação, atualize `publicadoEm` e `novidades` no bloco `CONFIG` do `index.html`.

Procedimentos secundários sem principal compatível no mês (quimioterapia, hormonioterapia e radioterapia) não são cobrados: o valor fica zerado e o site avisa.

## Juntar planilhas da equipe

Na tela *Juntar planilhas da equipe*, escolha dois ou mais .xlsx exportados por usuários do mesmo projeto. O site une só os arquivos com o mesmo nome de uso, reduz a um os registros com o mesmo `id` (vale o do arquivo exportado por último), avisa quando as decisões do projeto diferem e quando há possíveis duplicidades entre usuários, e recalcula tudo. Planilhas na versão sem prontuário não podem ser unidas.
