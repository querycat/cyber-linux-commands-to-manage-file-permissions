# Permissões de arquivos no Linux

## Descrição do projeto

Este projeto simula uma tarefa de um profissional de segurança que dá suporte à equipe de pesquisa de uma organização. O objetivo é auditar as permissões atuais dos arquivos e do diretório em `/home/researcher2/projects` e ajustálas para que reflitam a autorização correta de cadausuário. Usando comandos Linux como `ls -la` e `chmod`, verifiquei a string de permissões de cada item, identifiquei acessos que violam a política da organização, como permissão de gravação para **"outros"**, e corrigi essas permissões. O resultado é um ambiente de arquivos mais seguro, em que apenas os usuários e grupos autorizados podem ler, gravar ou executar cada recurso.

## Verificar detalhes de arquivos e diretórios

Para verificar as permissões atuais de arquivos e diretórios no Linux, uso o comando `ls -la`, que lista todos os itens de um diretório, incluindo arquivos ocultos, junto com detalhes como permissões, proprietário, grupo, tamanho e data de modificação. Executando esse comando no diretório `/home/researcher2/projects`, obtive a seguinte saída:

```bash

researcher2@Ubuntu-2404:~/projects$ ls-la
total 20
drwxr-xr-x 3 researcher2 researcher2 4096 sep 24 09:00
drwxr-xr-x 6 researcher2 researcher2 4096 sep 24 09:00
-rw-rw-rw- 1 researcher2 researcehr2 120 sep 24 09:00 project_k.txt
-rw-r----- 1 researcher2 researcher2 120 sep 24 09:00 project_m.txt
-rw--rw-r-- 1 researcher2 researcher2 120 sep 24 09:00 project_r.txt
-rw-rw-r-- 1 researcher2 researcher2 120 sep 24 09:00 project_t.txt
-rw--w---- 1 researcher2 researcher2 120 sep 24 09:00 project_x.txt
drwxr-x--- 3 researcher2 reseacher2 4096 sep 24 09:00 drafts

```

A primeira coluna da saída (por exemplo, `-rw-rw-rw` ou `drwxr-x---`) é a cadeia de 10 caracteres que representa o tipo do item e suas permissões, detalhada na próxima seção.

## Descrever a string de permissões

Tomando como exemplo o arquivo project_r.txt, cuja cadeia de permissões é -rw-rw-r--, cada um dos 10 caracteres tem um significado específico:

- **O 1° caractere** (`-`): indica o tipo do item. "`-`" significa arquivo comum; "`d`" indicaria um diretório.
- **Caracteres 2 a 4** (`rw-`): permissões do usuário (proprietário). leitura (`r`) e gravação (`w`), sem execução (`-`).
- **Caracteres 5 a 7** (`rw-`): permissões do grupo. leitura (`r`) e gravação (`w`), sem execução (`-`).
- **Caracteres 8 a 10** (`r--`): permissões de outros (qualquer outro usuário do sistema). apenas leitura (`r`), ssem gravação nem execução (`-`).

Em resumo, essa cadeia mostra que o proprietário e o grupo podem ler e gravar o arquivo, enquanto qualquer outro usuário do sistema pode apena lê-lo.

## Alterar permissões de arquivos

A política da organização não permite que "outros" tenham acesso de gravação a nenhum arquivo. Analisando a lista de permissões, o arquivo `project_k.txt` (`rw-rw-rw-`) é o único que concede permissão de gravação a outros. Para remover essa permissão, usei o comando:

```bash

researcher2@Ubuntu-24-04:~projects$ chmod o-w projects_k.txt
researcher2@Ubuntu-24-04:~projects$ ls -la project_k.txt
-rw-rw-r-- 1 researcher2 researcher2 120 sep 24 09:00 project_k.txt

```

O comando `chmod o-w` remove (`-`) a permissão de gravaçãp (`w`) da categoria "outros" (`o`), mantendo as permissões de leitura e gravação do usuário e do grupo intactas. A nova cadeira de permissões, `rw-rw-r--`, confirma que **outros** agora só podem ler o arquivo.

## Alterar permissões de arquivos ocultos

O arquivo `.project_x.txt` foi arquivado pela equipe de pesquisa, por isso é um arquivo oculto (nomes de arquivos que começam com ponto são ocultos no Linux). Suas permissões originais eram `rw--w----`, ou seja, o usuário podia ler e gravar, e o grupo só podia gravar (sem poder ler), o que não corresponde à política exigida. A política determina que ninguém deve ter permissão de gravação, mas o usuário e grupo devem poder ler o arquivo. Usei o comando:

```bash

researcher2@Ubuntu-24-04:~projects$ chmod 440 .project_x.txt
researcher2@Ubuntu-24-04:~projects$ ls -la .project_x.txt
-r--r----- 1 researcher2 researcher2 120 sep 24 09:00 .project_x.txt

```

O comando `chmod 440` define as permissões usando notação octal: `4` (leitura), `4` (leitura) para grupo e `0` (nenhuma permissão) para outros. O resultado, `r--r-----`, mostra que apenas usuário e grupo podem ler o arquivo, e ninguém tem permissão de gravação.

## Alterar permissões de diretórios

Os arquivos e diretórios em `/home/researcher2` pertencem ao usuário `researcher2`. A política estabelece que apenas researcher2 deve ter acesso ao diretório drafts e ao seu conteúdo, ou seja, grupo e outros não devem ter nenhuma permissão sobre ele. AS permissões eram `rwxr-x---`. Usei o comando:

```bash

researcher2@Ubuntu-24-04:~projects$ chmod 700 drafts
researcher2@Ubuntu-24-04:~projects$ ls -la
drwx------ 3 researcher2 reseacher2 4096 sep 24 9:00 drafts

```

O comando `chmod 700` concede ao proprietário leitura, gravação e execução, e remove todas as permissões de grupo (`0`) e de outros (`0`). O resultado, `rwx------`, confirma que somente researcher2 pode acessar, listar e modificar o conteúdo do diretório `drafts`.

## Resumo

Neste projeto, atuei como profissional de segurança responsável por auditar e corrigir as permissões de arquivos e diretórios usados pela equipe de pesquisa. Primeiro usei ls -la para verificar as permissões existentes em `/home/researcher2/projects` e interpretei a cadeia de 10 caracteres de cada item para entender quem tinha acesso de leitura, gravação e execução. Em seguida, identifiquei violações da organização, como permissão de gravação para outros em `project_k.txt` e permissões incorretas no arquivo oculto `.projects_x.txt`, e usei o comando `chmod`, tanto em notação simbólica quanto octal, para corrigi-las. Por fim, restringi o diretório `drafts` para que apenas o proprietário `researcher2` tivesse acesso. Essas ações demonstram como o controle de permissões no Linux é usado na prática para reduzir riscos e garantir que apenas usuários autorizados tenham acesso aos recursos corretos.