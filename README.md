# Dicas sobre Desenvolvimento de Software com IA

Passo a passo básico para desenvolver software com apoio de IA, direto no VS Code, no Windows. Todos os scripts deste guia devem ser arquivos `.bat`.

1. Crie conta em mais de uma IA (DeepSeek, Claude, ChatGPT etc., ou as que couberem no seu bolso $$).

2. Instale no [Visual Studio Code](https://code.visualstudio.com/) a extensão da IA que for usar, por exemplo a [extensão do Claude Code](https://marketplace.visualstudio.com/items?itemName=anthropic.claude-code) ([documentação](https://code.claude.com/docs/en/vs-code)). Dê preferência à extensão em vez da CLI (linha de comando): o chat fica integrado ao editor, junto dos arquivos do projeto.

3. Na pasta do projeto, crie um arquivo em Markdown com as ideias e especificações iniciais (objetivos, clientes, tecnologias, regras de negócio iniciais etc.).

4. Abra o VS Code na pasta do projeto e peça à IA que leia o projeto. No Claude, use no chat do VS Code o comando `/init`.

5. Peça a uma IA (pode ser o Claude) que organize o arquivo de documentação, com um índice (TOC, *Table of Contents*) no início e seções com títulos em Markdown e emojis.

Abaixo temos um **exemplo** de diagrama feito com mermaid

   1. A documentação pode conter diagramas. Faça-os em [mermaid](https://mermaid.js.org/); se preferir, coloque-os em anexo, fora do documento principal.

      ```mermaid
      flowchart TD
          A[Item do backlog] --> B[IA implementa]
          B --> C{Testes passam?}
          C -->|Sim| H{Revisão e critérios de aceite OK?}
          C -->|Não| E[Debug]
          E --> C
          H -->|Não| B
          H -->|Sim| D[Atualizar backlog, documentação e horas]
          D --> F[Commit]
          F --> G[Próximo item]
      ```

6. Peça a TODAS as IAs que respondam no chat e escrevam a documentação em português do Brasil. Para não repetir o pedido a cada conversa, registre essa regra no arquivo de instruções do projeto (no Claude, o `/init` do item 4 cria o `CLAUDE.md`).

7. Peça a uma das IAs que crie os seguintes arquivos:

   1. `CHANGELOG.md`, com o registro das mudanças e um resumo das horas medidas no topo, baseado no registro de tempo descrito abaixo.

   2. Documentação, em Markdown, seguindo o que foi definido no item 5 (índice, emojis e diagramas em mermaid).

   3. `BACKLOG.md`, com identificador, objetivo, situação (planejado, pendente, em andamento ou finalizado), prioridade, dependências, complexidade (baixa, média ou alta) e critérios de aceite de cada item. Os critérios de aceite devem descrever resultados verificáveis, para deixar claro quando a tarefa está concluída.

   4. `HORAS.md`, com a data, o identificador do item do backlog, a duração e a origem de cada registro (cronômetro ou informação fornecida por você). Desconte as pausas. Estimativas devem ficar separadas das horas medidas e não entrar no total realizado. A IA não deve inventar durações; se não houver medição, registre "não informado".

8. Peça à IA que crie um script `.bat` que abra no navegador um dashboard em HTML com todo o backlog. No topo, coloque botões para filtrar por situação (planejado, pendente, em andamento ou finalizado) e por complexidade (baixa, média ou alta). Use o `BACKLOG.md` como fonte única: a cada execução, o `.bat` deve acionar a geração do HTML com os dados atuais incorporados e, depois, abri-lo no navegador. Assim, o HTML não precisa ler arquivos locais pelo navegador. Para atualizar a visualização, execute o `.bat` novamente; não edite os dados diretamente no HTML. Documente os pré-requisitos da ferramenta usada para gerar o dashboard.

9. Documente o propósito das funções e classes públicas, as regras de negócio e as decisões que não são evidentes pelo código. Use docstrings ou comentários conforme a linguagem; evite comentários que apenas repitam o nome de uma função ou a operação da linha seguinte. Adicione uma descrição no início do arquivo quando ela ajudar a explicar sua responsabilidade. O exemplo abaixo é em Python:

   ```python
   def pode_cancelar_pedido(status: str) -> bool:
       """Permite cancelar apenas pedidos que ainda não foram enviados."""
       return status in {"pendente", "em_preparacao"}
   ```

10. Escreva prompts simples e diretos, com tarefas pequenas, para facilitar a conclusão dentro do limite de uso do seu plano. Considere um item concluído quando os critérios de aceite forem atendidos, os testes pertinentes passarem, as alterações forem revisadas e os registros forem atualizados. Ao terminar, peça: "Atualize o backlog, a documentação, o changelog e as horas medidas". Se houver um repositório Git local, revise as alterações e peça um commit com uma mensagem que descreva a mudança.

    Exemplo de prompt para implementar um item do backlog:

    > Leia a documentação e o item BL-003 do BACKLOG.md. Implemente o cancelamento de pedidos. Critérios de aceite: pedidos pendentes ou em preparação podem ser cancelados; pedidos enviados não podem ser cancelados; a tentativa de cancelar um pedido enviado deve exibir uma mensagem clara e manter seu estado. Siga os padrões do projeto e limite as mudanças a esse item. Crie ou ajuste os testes para os cenários descritos e execute a validação pelo script .bat do projeto. Ao terminar, apresente as alterações, os resultados dos testes e eventuais pendências para revisão. Após a revisão, marque o item como finalizado se todos os critérios forem atendidos e atualize a documentação, o changelog e as horas com base apenas nos registros disponíveis. Se não conseguir executar algum teste, informe a limitação.

11. Peça à IA que crie um script `.bat` que dispare de uma só vez toda a suíte de testes disponível no projeto (unitários, regressão, segurança e outros) e gere um arquivo de log por execução, com data e hora no nome. O script deve indicar se houve falha e retornar um código de saída diferente de zero nesse caso. Execute-o após cada item implementado e antes do commit. Corrija as falhas e execute novamente antes de considerar o item concluído.

12. Peça à IA que crie um script `.bat` para rodar o software que você está desenvolvendo. Por exemplo, um único script que inicie o servidor web e o banco de dados e abra o navegador na URL correta.

**Nota 1**: não recomendo começar a codificar um item de backlog com uma IA e continuar com outra. O que uma IA começou, é bom que ela termine; por isso os itens de backlog têm que ser pequenos, e o ideal é usar uma IA por item.

**Nota 2**: instale também *skills*, servidores MCP e plugins para ampliar o que a IA consegue fazer:

- [Skills](https://code.claude.com/docs/en/skills): conjuntos de instruções reutilizáveis que ensinam a IA a executar tarefas específicas.
- [MCP](https://modelcontextprotocol.io) (*Model Context Protocol*): padrão que conecta a IA a ferramentas e serviços externos, como navegador, banco de dados e APIs ([como usar no Claude Code](https://code.claude.com/docs/en/mcp)).
- Plugins, por exemplo o [superpowers](https://github.com/obra/superpowers), um conjunto de skills para o fluxo de desenvolvimento.
