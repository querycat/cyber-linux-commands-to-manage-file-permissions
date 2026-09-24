# Permissões de Arquivos no Linux

## Sobre o projeto

Projeto de estudo e portifolio de cibersegurança que simula uma auditoria de permissões de arquivos e diretórios em sum sistema Linux, com correção de acessos não autorizados usando `chmod`.

## Estrutura do repositório e ordem de leitura sugerida

├── README.md
├── docs
│   ├── current-file-permissions.md
│   └── example-file-permissions-in-linux.md
└── result
    └── file-permissions-in-linux.md

| Ordem | Arquivo | Propósito |
|---|---|---|
| 1 | [`current-file-permissions.md`](docs/current-file-permissions.md) | Levantamento das permissões originais dos arquivos e do diretório antes de qualquer correção |
| 2 | [`example-file-permissions-in-linux.md`](docs/example-file-permissions-in-linux.md) | Exemplo que demonstra como o relatório deve ser estruturado. |
| 3 | [`file-permissions-in-linux.md`](result/file-permissions-in-linux.md) | Relatório completo: descrição do projeto, explicação da cadeia de permissões, comandos usados e resumo |

## Cenário

Como profissional de segurança dando suporte à equipe de pesquisa de uma organização, a tarefa é examinar as permissões existentes no diretório `/home/researcher2/projects`, verificar se elas correspondem à autorização que deveria ser concedida e corrigir qualquer acesso indevido.

## Ambiente
Diretório auditado: `/home/researcher2/projects`
5 arquivos (um deles oculto) e 1 subdiretório (`drafts`)
Proprietário de todos os itens: `researcher2`

## Metodologia
    1. **Levantamento**: usar `ls -la` para listar todos os itens do diretório, incluindo arquivos ocultos, e registrar a cadeia de permissões de cada um ([`current-file-permissions.md`](docs/current-file-permissions.md)).
    2. **Interpretação**: decompor cada cadeia de 10 caracteres (tipo do item + permissões de usuário, grupo e outros) para entender o acesso real concedido.
    3. **Comparação com a política**: confrontar as permissões encontradas com as regras de segurança da organização — nenhuma gravação para "outros", leitura restrita para o arquivo arquivado, e acesso exclusivo do proprietário ao diretório `drafts`.
    4. **Correção**: aplicar `chmod`, em notação simbólica ou octal conforme o caso, apenas nos itens que divergiam da política, evitando alterar permissões que já estavam corretas.
    5. **Validação**: reexecutar `ls -la` após cada alteração para confirmar que o resultado corresponde à permissão esperada.
    6. Documentação: registrar comando, saída e justificativa de cada mudança no relatório final ([`docs/file-permissions-in-linux.md`](result/file-permissions-in-linux.md)).


## Permissões encontradas

|Item | Permissões originais | Usuário | Grupo | Outros |
|---|---|---|---|---|
| `project_k.txt` | `rw-rw-rw-` | rw | rw | rw |
| `project_m.txt` | `rw-r-----` | rw | r | — | 
| `project_r.txt` | `rw-rw-r--` | rw | rw | r |
| `project_t.txt` | `rw-rw-r--` | rw | rw |r |
| `.project_x.txt` | `rw--w----`| rw | w | — |
| `drafts/` | `rwxr-x---`	| rwx |r-x	| — |

## Política de segurança aplicada

    1. Nenhum arquivo pode ter permissão de gravação para "outros".
    2. O arquivo arquivado `.project_x.txt` deve permitir apenas **leitura** para usuário e grupo, sem gravação para ninguém.
    3. Apenas o proprietário `researcher2` pode acessar o diretório `drafts` e seu conteúdo.

``
## Comandos usados

```bash
# 1. Remover gravação de "outros" em project_k.txt
chmod o-w project_k.txt
# resultado: rw-rw-r--

# 2. Corrigir .project_x.txt: leitura para usuário/grupo, sem gravação para ninguém
chmod 440 .project_x.txt
# resultado: r--r-----

# 3. Restringir drafts/ apenas ao proprietário
chmod 700 drafts
# resultado: rwx------
```

## O que a cadeia de 10 caracteres representa

Exemplo: `` ``

| Posição | Caractere(s) | Significado |
|---|---|---|
| 1	| `-` | Tipo do item (`-` = arquivo, `d` = diretório) |
| 2–4 |	`rw-` | Permissões do usuário (proprietário) |
| 5–7 | `rw-` | Permissões do grupo |
| 8–10 | `r--` | Permissões de outros |

## Habilidades demonstradas

- Verificação de permissões com `ls -la`
- Interpretação da cadeia de permissões de 10 caracteres
- Modificação de permissões com `chmod` (notação simbólica e octal)
- Gerenciamento de arquivos ocultos no Linux
- Aplicação do princípio do menor privilégio


## Aviso

Este projeto é baseado em um cenário educacional e tem finalidade exclusivamente de portfólio/estudo.

## Autoria

querycat — em transição de carreira de desenvolvimento de software para cibersegurança, com foco em unir experiência em pagamentos e antifraude ao conhecimento de segurança.

[GitHub](https://github.com/querycat) · [LinkedIn](https://www.linkedin.com/in/maysa-pereira/)