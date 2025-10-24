# desafio-aws-ec2-arquitetura
Desafio AWS: Gerenciamento de EC2, anotações técnicas e diagrama de arquitetura (S3/EC2/Lambda/EBS).

🚀 Desafio: Gerenciamento de Instâncias EC2 e Arquitetura AWS
Este repositório é o entregável do desafio que visa consolidar conhecimentos em gerenciamento de instâncias EC2 na AWS e documentação técnica. O projeto inclui um diagrama de arquitetura que integra EC2 com S3, AWS Lambda e EBS.
🎯 1. Objetivos do Desafio
Ao desenvolver este projeto, os seguintes objetivos foram alcançados:
 * Aplicar Conceitos: Utilização dos serviços AWS (EC2, S3, Lambda, EBS) em um ambiente prático.
 * Documentar Processos: Criação de um repositório organizado e um README.md detalhado para documentação técnica.
 * Utilizar GitHub: Uso da plataforma para versionamento e compartilhamento do material de apoio.
🏗️ 2. Arquitetura Proposta: S3, EC2, Lambda e EBS
O diagrama a seguir ilustra uma arquitetura para um cenário de processamento de arquivos que exige tanto capacidade serverless (Lambda) para tarefas leves quanto capacidade dedicada (EC2) para processamento pesado.
![Diagrama da Arquitetura AWS](images/Diagrama AWS.drawio.png)
2.1. Descrição e Fluxo da Solução
1. Usuário (User) e Amazon S3:
   * Função: O Amazon S3 (Simple Storage Service) é utilizado como o ponto de entrada (Bucket de Upload) para o armazenamento de objetos brutos (arquivos, logs, mídia).
   * Fluxo: O User inicia o processo ao realizar o Upload do arquivo para o S3.
2. Amazon EC2 (Elastic Compute Cloud):
   * Função: A instância EC2 é o principal recurso de processamento (Processamento). Ela é necessária para tarefas que exigem um longo tempo de execução, recursos de CPU/Memória dedicados, ou softwares que precisam ser instalados em um sistema operacional específico.
   * Fluxo: Após o Upload no S3, a EC2 é notificada (ou acionada por um serviço intermediário como SQS ou SNS) para iniciar o processamento do arquivo.
3. Amazon EBS (Elastic Block Store):
   * Função: O EBS fornece volumes de armazenamento em bloco persistente que são anexados à instância EC2. É essencial para o armazenamento do sistema operacional e de quaisquer dados que precisem de durabilidade e baixa latência para o EC2.
   * Fluxo: O fluxo Armazenamento representa a dependência direta do EC2 no seu volume EBS para persistência dos dados e do sistema.
4. AWS Lambda Function:
   * Função: A Lambda Function é responsável por tarefas leves e reativas. No fluxo, ela é acionada após o processo principal.
   * Fluxo: Após o EC2 concluir o Processamento e o Armazenamento dos resultados no EBS, a Lambda é acionada (pela própria EC2, por um evento de shutdown da EC2, ou por um evento no EBS/S3 de resultados) para realizar ações como:
   * Enviar notificações ao usuário.
   * Limpar metadados.
   * Atualizar status em um banco de dados.
📚 3. Insights e Anotações sobre Gerenciamento de EC2
Esta seção consolida os aprendizados e pontos-chave adquiridos durante a prática com o Amazon EC2, focando em como configurar e gerenciar as instâncias de forma eficiente.
3.1. Provisionamento e Configuração
  Tópico                                      Insights Adquiridos
  - AMI e Tipo de                             A escolha da AMI (Amazon Machine Image) e do Tipo de Instância (ex: t2.micro para testes) é a base. É crucial entender a diferença entre instâncias burstables e dedicadas.
    Instância

  - Chave de Acesso (Key Pair)                Essencial para o acesso SSH. O arquivo .pem deve ser armazenado de forma segura e com permissões restritas (chmod 400).

  - Grupos de Segurança (Security Groups)     Atuam como um firewall virtual. É vital aplicar o princípio do menor privilégio, permitindo o tráfego de entrada apenas nas portas e IPs necessários (ex: Porta 22 para SSH, Porta 80 para HTTP).
    
3.2. Armazenamento Persistente (EBS)
  * Durabilidade: O volume EBS fornece durabilidade e persistência de dados, diferente do armazenamento de instância (Ephemeral Storage). Mesmo que a instância EC2 seja encerrada, o volume EBS pode ser desanexado e reanexado a outra instância.
  * Backup: A criação de Snapshots do EBS é o método primário para backup pontual do volume, garantindo a recuperação em caso de falha.
3.3. Comandos e Ações Chave
  * Conexão SSH: O comando básico para acesso é ssh -i minha-chave.pem ec2-user@<IP_PÚBLICO>.
  * Gerenciamento do Ciclo de Vida: A diferença entre Parar (Stop) e Encerrar (Terminate) uma instância é crítica. Parar mantém o volume EBS e o IP Público (se alocado) e é ideal para economizar custos. Encerrar apaga a instância e, por padrão, o volume de boot.

    
