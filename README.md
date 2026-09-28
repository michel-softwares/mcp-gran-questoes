# MCP para criação de cadernos de questões na plataforma Gran Questões

O Traki, a corujinha robótica do Track Concursos, conecta um assistente de IA ao catálogo de filtros de questões do Gran Questões. Você informa o assunto que está estudando, e o assistente usa as ferramentas MCP para encontrar os filtros correspondentes e entregar um link de questões filtradas por assunto, banca, escolaridade e outros critérios disponíveis.

<p align="center">
  <img src="assets/traki-apresentando-gran.png" width="248" alt="Traki apresentando o Gran Questões">
  &nbsp;&nbsp;&nbsp;&nbsp;
  <img src="assets/traki-gran.png" width="190" alt="Traki com os superpoderes do Gran Questões">
</p>

<p align="center"><sub>Traki apresentando o Gran Questões e o Traki com os superpoderes do Gran Questões.</sub></p>

<p align="center">
  <a href="https://claude.ai"><img src="https://img.shields.io/badge/Claude-MCP%20Ready-D97757?style=flat&logo=anthropic&logoColor=white&labelColor=111827" alt="Claude MCP Ready"></a>
  <a href="https://modelcontextprotocol.io"><img src="https://img.shields.io/badge/MCP-Streamable%20HTTP-2563EB?style=flat&logo=json&logoColor=white&labelColor=111827" alt="MCP Streamable HTTP"></a>
  <a href="https://aws.amazon.com"><img src="https://img.shields.io/badge/AWS-EC2%20Hosted-FF9900?style=flat&logo=amazon-aws&logoColor=white&labelColor=111827" alt="AWS EC2 Hosted"></a>
  <a href="LICENSE"><img src="https://img.shields.io/badge/Licen%C3%A7a-MIT-10B981?style=flat&labelColor=111827" alt="Licença MIT"></a>
</p>

## Para que serve

Essa ferramenta MCP foi criada para ajudar quem estuda pelo Track Concursos ou por outras plataformas a criar cadernos de questões com auxílio de uma IA. Em vez de selecionar manualmente disciplina, assunto, banca e escolaridade no Gran Questões, o estudante informa o tópico do edital, o assistente de IA interpreta o pedido, usa o MCP para localizar os filtros disponíveis e entrega um link para as questões correspondentes. Assim, o estudante gasta menos tempo criando um caderno de questões e mais tempo resolvendo questões (estudando).

## Como esse projeto é possível?

Plataformas como o Qconcursos e o Gran Concursos permitem aplicar filtros diretamente pela URL. Cada assunto, banca e outros tipos de filtros, possuem um identificador. Com esses IDs combinados na URL, é possível montar um link que já abre a página com as questões filtradas.

É aí que entra a IA: ela entende o tópico que o estudante quer praticar e, com a ajuda do MCP, encontra os IDs correspondentes no catálogo de filtros do Gran Questões. Assim, consegue combinar dezenas de filtros em segundos e entregar um link pronto para estudar, sem que o estudante precise selecionar cada opção manualmente. Este projeto usa esse mecanismo para gerar links do **Gran Questões**.

## Como funciona

1. **Você descreve o tópico.** Pode escrever diretamente no Claude ou colar um tópico do edital verticalizado do [Track Concursos](https://trackconcursos.vercel.app/), com disciplina, banca e escolaridade.
2. **O Claude interpreta o pedido** e chama o MCP do Traki, hospedado na AWS.
3. **O servidor consulta seu catálogo local de filtros do Gran.** Ele procura os assuntos na árvore de disciplinas, seleciona os IDs encontrados e monta uma URL de questões com os filtros solicitados.
4. **O Claude confere o resultado.** Ele compara os assuntos retornados com o tópico pedido e observa itens não encontrados ou correspondências imprecisas.
5. **Se necessário, o Claude consulta o MCP novamente**, usando termos mais específicos ou uma nova busca. O servidor refaz a seleção, e o Claude analisa a nova resposta antes de apresentar o link.

A busca no catálogo e a montagem da URL acontecem no servidor AWS. Apenas a interpretação do pedido e a revisão da pertinência dos filtros dependem do assistente de IA, o que economiza muitos TOKENS e permite muitas consultas na IA do Claude versão gratuita! Os scripts foram projetados para selecionar os filtros relevantes ao tópico estudado, evitando incluir assuntos de fora ou deixar de fora filtros importantes. Ainda assim, podem ocorrer erros, especialmente quando o tópico do edital é amplo ou ambíguo, ou quando há poucos filtros correspondentes no Gran Questões. 
O projeto **não cria um caderno salvo na conta do Gran**: ele gera um link para a página de questões já filtradas.


### Exemplo de utilização

> “Traki, estou estudando este tópico do meu edital "Diagramas lógicos", banca FCC, nível médio, quero questões dos últimos 10 anos. Gere um caderno do Gran”

O Claude ou outro assistente de IA, irá se conectar ao MCP e retornar uma resposta para o seu pedido:

🎯 Caderno Gran Questões Gerado com Sucesso! 
[🔗 Clique aqui para abrir o Caderno no Gran Questões](https://questoes.grancursosonline.com.br/questoes?desatualizada=0&anulada=0&assunto=406162%2C425297%2C425298%2C425299%2C425300&banca=92&anos=2017%2C2018%2C2019%2C2020%2C2021%2C2022%2C2023%2C2024%2C2025%2C2026&nivel=2)

O assistente pode pesquisar os assuntos, gerar a URL, revisar os filtros retornados e refazer a busca se encontrar alguma divergência. Caso não encontre um filtro adequado, deve informar a limitação em vez de presumir uma correspondência.

## Conectar ao Claude

Você pode utilizar esse MCP gratuitamente através do Claude Desktop ou Claude.ai.

No Claude, procure pela opção Personalização → Conectores → Adicionar → Adicionar um Conector personalizado → Coloque um nome como **Traki Gran Questões** → na URL do MCP adicione:

```text
https://tocadotraki.vercel.app/mcp-gran
```

*(Ou diretamente pelo endpoint do servidor: `https://18-221-134-140.sslip.io/mcp`)*


Depois vincule, ative as permissões do MCP no Claude e pode pedir em qualquer chat: “Traki, estou estudando esse tópico do meu edital [seu tópico de estudo], a banca é [sua banca], nível médio, quero questões dos últimos 10 anos.” e o Claude irá se conectar às ferramentas do MCP e gerar o caderno para você automáticamente.

### Uso com o Track Concursos

No seu edital verticalizado do Track Concursos, na aba das disciplinas o atalho **Ctrl + clique esquerdo do mouse** em um tópico copia um pedido com o contexto de estudo já pronto apenas para colar no Claude. 
Cole esse texto no Claude conectado ao Traki. O contexto ajuda a distinguir assuntos parecidos e a aplicar banca e escolaridade quando informadas. 
Você também pode pedir outros filtros disponíveis nesta integração, por exemplo:

- “Quero questões da banca IBFC, nível médio.”
- “Quero questões dos últimos 10 anos.” o traki gerará um link com questões filtradas dos últimos 10 anos
- “Quero apenas questões do cargo de Policial Militar, banca cebraspe, nível superior, dos últimos 10 anos.” O filtro do cargo Policial Militar, da banca cebraspe, da escolaridade superior e dos últimos 10 anos serão aplicados.

## Ferramentas MCP

| Ferramenta | O que faz |
| --- | --- |
| `gerar_caderno_gran` | Seleciona assuntos e gera a URL de questões com filtros. |
| `pesquisar_assuntos_gran` | Busca assuntos no catálogo para conferir nomes e caminhos. |
| `listar_disciplinas_gran` | Lista disciplinas disponíveis no catálogo. |
| `listar_bancas_gran` | Consulta bancas disponíveis. |

O catálogo é consultado localmente pelo servidor; cada pedido não faz uma busca em tempo real no site do Gran Questões. Os filtros podem mudar na plataforma, por isso convém conferir o resultado ao abrir o link.


## Sobre o projeto

O Traki faz parte do [Track Concursos](https://trackconcursos.vercel.app/) e não possui nenhum vínculo oficial com o Gran Questões. Os links gerados apontam para `questoes.grancursosonline.com.br`, portanto para visualização deles é preciso estar logado na plataforma `questoes.grancursosonline.com.br`. 

Licença: [MIT](LICENSE).

## Apoie o projeto

O Traki e o Track Concursos são projetos feitos para ajudar concurseiros a estudar com mais organização. Se eles forem úteis para você, há algumas formas de apoiar:

- ⭐ Deixe uma estrela [neste repositório do MCP](./) e no [repositório do Track Concursos](https://github.com/michel-softwares/track-concursos). Isso ajuda outras pessoas a encontrar os projetos.
- 🌐 Conheça e compartilhe o [site oficial do Track Concursos](https://trackconcursos.vercel.app/).
- ☕ Se quiser contribuir com a manutenção desse projeto e as próximas melhorias, a chave Pix é **`michelaraujo100@gmail.com`**.

Obrigado pelo apoio e bons estudos!
