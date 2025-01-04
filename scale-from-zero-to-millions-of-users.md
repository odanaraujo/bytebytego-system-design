# Configuração de servidor único 👨‍💻

## Prefácio 🧵

Este repositório visa me auxiliar nos estudos de system design, especificamente no curso de [system design - bytebytego]( https://bytebytego.com/courses/system-design-interview).

## Configuração de servidor único 💻

A figura a seguir mostra algo simples, onde todo o sistema está sendo executado em apenas um servidor.

![servidor único](/assets//images/image.png)

1. Os usuários acessam o site por meio de um domínio. O sistema de nomes de domínio (DNS) é um servidor fornecido por terceiros e não hospedados por nossos servidores.
2. O endereço de IP (internet protocol) é retornado ao navegador ou app. 
3. Ao obter o IP, as solicitações do Hypertext Transfer Protocol (HTTP) são enviadas
4. O servidor web retorna páginas HTML ou uma resposta JSON para renderização.

## Bases de dados 📇

Com o crescimento de base de usuários, um servidor não é suficiente e precisamos de vários servidores:  um para o tráfego web/app e outro para banco de dados.

![design de banco de dados](/assets//images/image-2.png)

### Quais banco de dados usar? 👒

Temos o tradicional banco de dados relacional (SQL) e o não relacional (NoSQL).

Os bancos de dados relacionais também são chamados de RDBMS(relational database management system) ou apenas banco de dados SQL. Os mais populares são oracle, MySQL, postgreSQL, etc. 

Os bancos de dados não relacionais também são chamados de bancos de dados NoSQL. Os mais populares são MongoDB, Cassandra, Neo4j, etc. Eles são agrupados em 4 categorias:

- armazenamentos de chave-valor.
- repositorios de grafos.
- repositorios de colunas.
- repositorios de documentos.

Os NoSQL podem ser a escolha certa se:

- Aplicação requer latência super baixa.
- Os dados não são estruturados ou não tem dados relacionais.
- É necessário apenas serializar e desserializar dados (JSON, HTML, YAML, etc).
- Precisa armazenar uma grande quantidade de dados.

## Dimensionamento vertical x horizontal 🚦

### Vertical

O dimensionamento vertical serve para quando precisamos adicionar mais "energia" (CPU, RAM, etc) aos servidores. 

### Horizontal

Já o dimensionamento horizontal, conhecido como "expansão", permite adicionar mais servidores ao pool de recursos.

Quando o tráfego é baixo, o dimensionamento vertical é o ideal, pela sua simplicidade como sua maior vantagem. Infelizmente, ela vem com várias limitações. 

- Há limites para a adição de CPU e RAM em um servido. 
- Não há failover* e redundância. Se um servidor cair, o site/app ficará completamente inativo com ele.

O dimensionamento horizontal é o mais desejável quando há uma grande escala. Exemplo:
No desenho acima, caso o servidor caia, os usuários não conseguiram ter acesso as informações da aplicação. Em outros cenários, se vários usuários acessarem simultaneamente o servidor e ele atingir o limite de carga, os usuários geralmente terão uma resposta mais lenta ou nem conseguirão ter acesso ao servidor. Um balanceador de cargas é uma ótima opção para esses cenários.

***failover** é uma técnica de recuperação de desastre que permite que sistemas e redes continuem funcionando mesmo quando há falhas ou interrupções.

## Balanceador de carga ⚖️

![design de balanceador de cargas](/assets/images/image-3.png)

Um balanceador de carga distribui   uniformemente o tráfego de entrada entre os servidores web definidos em um conjuntos com balanceamento, como na imagem acima.

Os usuários se conectam diretamente ao IP público do balanceador de carga. Dessa forma, usuários não se conectam diretamente pelos clientes. Para melhor segurança, IP privados são usados para a comunicação dos servidores. Esse IP privado será acessível apenas entre os servidores na mesma rede; no entanto, é inacessível pela internet. O balanceador também se comunicará com esses servidores através de um IP privado. 

Após adicionar um balanceador e um segundo servidor, resolvemos com sucesso o problema de failover e melhoramos a disponibilidade da camada de Web. 

- Se o servidor 1 ficar offline, todo o tráfego será redirecionado para o servidor 2. Também podemos adicionar um servidor Web íntegro ao pool de servidores para equilibrar a carga.
- Digamos que o tráfego aumente rapidamente e esses dois servidores não sejam suficientes, o balanceador pode resolver esse problema: basta adicionar mais um servidor ao pool de servidores Web e o balanceador começará a enviar de forma automática solicitações a ele. 

Agora que a camada Web está protegida, devemos pensar na camada de dados. A replicação de banco de dados é uma ótima técnica para resolver failover e redundância. 
