# Desafio - Proposta de solução

Realizei algumas pesquisas sobre o MongoDB, pois inicialmente, por ser baseado em arquivo texto, achava que se tratava de um Banco de Dados nada seguro. Achei um artigo que falava de um “apocalipse do MongoDB”, onde entre o final de 2016 e primeiro quarter de 2017, várias bases de dados foram hackeadas, onde os hackers faziam uma cópia da base para um servidor e apagavam a original, e para que a base fosse recuperada, exigiam um resgate em bitcoins. O questionamento do artigo é: A Base de dados é o problema? Ou quem configurou ela é o problema?
A maioria das instalações de mongoDB usavam as configurações default, que são facilmente achadas na internet e hackers conseguem a informação de bases inseguras usando mecanismos de buscas como ZoomEye(? Não cheguei a pesquisar sobre esse mecanismo de busca, ele é apenas citado no artigo).
Então, para evitar este tipo de situação, a empresa deve ter uma política de segurança bem definida. Algumas das medidas seriam:

Configurar autenticação no ambiente de produção; MongoDB dispõe de alguns mecanismos de autenticação e a empresa pode escolher o que mais se adequa à sua realidade. Não cheguei a pesquisar sobre cada mecanismo nem qual seria o melhor por causa do tempo para o desafio.
Criar um usuário administrador;
Criptografar todas as comunicações entre instâncias de MongoD e MongoS, assim como comunicações externas usando TSL/SSL.
Criptografar cada host de MongoDB

MongoDB ainda dispõe de Controle de acesso baseado em papéis, então, enquanto o administrador poderia ter acesso à tudo, cada microserviço poderia ter seu usuário específico, com papel com acesso somente leitura. Caso mais papéis precisem ser criados, entender bem esses papéis e o que cada um pode acessar ajuda para evitar vazamento de dados e uso indevido dos mesmos.
Também oferece criptografia de rede TLS/SSL
Além destas, medidas como firewall e VPNs aumentam a segurança das bases de dados.

MongoDB é bem flexível, o que, num contexto de uma startup, onde mudanças são constantes e modelos de negócios mudam para melhor se adequar à necessidade do consumidor, ter uma base de dados que permita que mudanças sejam feitas de forma rápida é extremamente competitivo e essencial para que o negócio ande conforme esperado.
Já no contexto de uma Bureau de Crédito, onde imagino que a modelagem é complexa, o mongo facilita no gerenciamento das modelagens.
É uma boa escolha no contexto de algoritmo de aprendizagem de máquina, pela flexibilidade e escalabilidade que oferece.
Com essas considerações, no meu ponto de vista, todas as 3 bases poderiam usar MongoDB, utilizando a arquitetura de microservices, onde cada serviço teria sua própria base. 

###Fontes/Pesquisas:

https://www.mongodb.com/blog/post/how-devops-microservices-and-mongodb-are-making-hsbc-simpler-better-and-faster
https://www.mongodb.com/blog/post/training-machine-learning-models-with-mongodb
https://www.mongodb.com/blog/post/deep-learning-and-the-artificial-intelligence-revolution-part-3
https://www.mongodb.com/blog/post/deep-learning-and-the-artificial-intelligence-revolution-part-4
https://imasters.com.br/banco-de-dados/mongodb-e-lgpd-quais-os-recursos-disponiveis
https://opensource.com/article/19/1/mongodb-security


Para o acesso à base A, teríamos um nanoserviço, que por meio de autenticação de um usuário somente leitura, onde seu único papel seria buscar as informações.
Uma característica destes dados é que não precisam de atualização constante, pois tratam-se de dados que não mudam com tanta frequência. Apesar de essa base não precisar de acesso rápido, seria perfeitamente viável armazenar estas informações em cache para daí ser acessado pelo front.

  Payload:
  
    CPF
    Nome
    Endereço
    CEP
    Endereço
    Número
    Complemento
    Bairro
    Cidade
    Estado
    País
      Lista de dívidas
      Valor contratado
    Data
    Qt parcelas
    Valor primeira parcela
    Tx Juros
    Saldo devedor
    Tipo

Para os acessos às bases B e C, comecei pesquisando sobre ElastichSearch e Redis, já que são duas tecnologias utilizadas pela empresa. Nunca trabalhei com nenhuma das duas tecnologias e nem sou familiarizada com os conceitos. Comecei pesquisando sobre cada uma das duas e verifiquei que as duas são consideradas banco de dados, sendo que ElasticSearch é utilizado para indexação em arquivos de textos e Redis é armazenamento de dados em cache, fazendo todo o gerenciamento do cache.

Para a consulta da base B, poderia ser construído um nanoserviço que obtém as informações necessárias e insere no Redis, já que são dados que não mudariam com tanta frequência, não sendo necessária uma visualização em tempo real.

Payload:

	  Nome
    CPF
    Idade
    Lista de bens
      Descricao do bem
      Valor
      Endereço
    CEP
      Endereço
      Número
      Complemento
      Bairro
      Cidade
      Estado
      País
    Fonte de renda
      CNPJ fonte pagadora
      Valor
      Data início

ElasticSerach poderia ser  usado no nanoserviço que alimenta a base C, onde poderia buscar em diversas fontes de dados todas as informações necessárias. O acesso ao ElasticSearch ficaria apenas no lado do servidor, não expondo os acessos em redes não seguras. Depois implementaria-se um serviço para buscar estes dados do mongoDB, pois já estariam organizados  A primeira consulta do CPF seria um pouquinho mais lenta, mas as próximas vezes seria necessário verificar a última data de evento que foi armazenado, e buscar os eventos do CPF a partir desta data, para deixar atualizado.

Payload:
	
	Data último acesso
	Movimentação financeira
		data
		tipo
		valor
		estabelecimento
	Última compra cartão de crédito
		data
		valor
		estabelecimento
		qt parcelas
		valor primeira parcela

Obs: Apesar de a solução não envolver a construção da base de dados, ficou um pouco mais fácil propor uma solução conhecendo a estrutura da base e sabendo as limitações/forças desta base, por isso a pesquisa para definir a base foi importante. Além disso, imagino que uma base externa não teria acesso direto ao dados, e sim teríamos uma camada de aplicação com apis disponibilizadas.

Pesquisas:

https://redis.io/topics/security
https://imasters.com.br/banco-de-dados/mongodb-e-lgpd-quais-os-recursos-disponiveis
https://www.mongodb.com/blog/post/training-machine-learning-models-with-mongodb
https://medium.com/data-hackers/comparando-elasticsearch-vs-mongodb-4b5932c613d9
https://www.sitepoint.com/caching-a-mongodb-database-with-redis/
https://dzone.com/articles/beautiful-marriage-mongodb-and
https://blog.caelum.com.br/buscas-eficientes-com-elasticsearch/
https://www.devmedia.com.br/o-que-e-elasticsearch/40207

