---
title: Rotação de certificado para Azure Database para MySQL
description: Conheça as próximas alterações das alterações ao certificado de raiz que afetarão a Base de Dados do Azure para o MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 01/18/2021
ms.openlocfilehash: 23fa3e93565066ce4b897bffe63164486efc179e
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102449888"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-mysql-single-server"></a>Compreender as alterações na alteração Root CA para Azure Database para MySQL Single Server

A Azure Database for MySQL Single Server concluiu com sucesso a alteração do certificado de raiz em **15 de fevereiro de 2021 (02/15/2021)** como parte das melhores práticas padrão de manutenção e segurança. Este artigo dá-lhe mais detalhes sobre as alterações, os recursos afetados e os passos necessários para garantir que a sua aplicação mantém a conectividade com o servidor da sua base de dados.

> [!NOTE]
> Este artigo aplica-se à [Base de Dados Azure para MySQL - Single Server](single-server-overview.md) ONLY. Para [a base de dados Azure para MySQL - Servidor Flexível,](flexible-server/overview.md)o certificado necessário para comunicar sobre o SSL é [DigiCert Global Root CA](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)
> 
> Este artigo contém referências ao termo _escravo_, um termo que a Microsoft já não utiliza. Quando o termo for removido do software, vamos removê-lo deste artigo.
>

## <a name="why-root-certificate-update-is-required"></a>Por que é necessária uma atualização do certificado de raiz?

A base de dados Azure para utilizadores do MySQL só pode utilizar o certificado predefinido para se ligar ao seu servidor MySQL, que está localizado [aqui.](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) No entanto, [o Fórum de Navegador da Autoridade de Certificados (CA)](https://cabforum.org/)publicou   recentemente relatórios de vários certificados emitidos por fornecedores de CA para não cumprirem.

De acordo com os requisitos de conformidade da indústria, os fornecedores de CA começaram a revogar os certificados de CA para CAs não conformes, exigindo que os servidores usassem certificados emitidos por AA em conformidade, e assinados por certificados ca desses CAs em conformidade. Uma vez que a Azure Database para o MySQL usou um destes certificados não conformes, precisávamos de rodar o certificado para a versão em conformidade para minimizar a ameaça potencial para os seus servidores MySQL.

O novo certificado é lançado e em vigor a partir de 15 de fevereiro de 2021 (02/15/2021). 

## <a name="what-change-was-performed-on-february-15-2021-02152021"></a>Que alteração foi realizada no dia 15 de fevereiro de 2021 (02/15/2021)?

Em 15 de fevereiro de 2021, o [certificado raiz BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) foi substituído por uma **versão compatível** do mesmo [certificado raiz BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) para garantir que os clientes existentes não precisam de alterar nada e não há qualquer impacto nas suas ligações com o servidor. Durante esta alteração, o [certificado raiz BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) não foi **substituído por** [DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) e essa alteração é adiada para permitir mais tempo para os clientes fazerem a mudança.

## <a name="do-i-need-to-make-any-changes-on-my-client-to-maintain-connectivity"></a>Preciso de fazer alguma alteração no meu cliente para manter a conectividade?

Não é necessária qualquer alteração do lado do cliente. se seguir a nossa recomendação anterior abaixo, ainda poderá continuar a ligar-se enquanto o **certificado BaltimoreCyberTrustRoot não for removido** do certificado combinado de CA. **Recomendamos não remover o BaltimoreCyberTrustRoot do seu certificado de CA combinado até novo aviso para manter a conectividade.**

### <a name="previous-recommendation"></a>Recomendação anterior

Para evitar que a disponibilidade da sua aplicação seja interrompida devido à revogação inesperada dos certificados, ou à atualização de um certificado que tenha sido revogado, utilize os seguintes passos. A ideia é criar um novo ficheiro *.pem,* que combine o cert atual e o novo e durante a validação do cert SSL será utilizado um dos valores permitidos. Consulte os seguintes passos:

* Baixe BaltimoreCyberTrustRoot & DigiCertGlobalRootG2 Root CA a partir dos seguintes links:

  * [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)
  * [https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)

* Geram uma loja de certificados CA combinada com certificados **BaltimoreCyberTrustRoot** e **DigiCertGlobalRootG2.**

  * Para os utilizadores java (Conector MySQL/J), execute:

    ```console
    keytool -importcert -alias MySQLServerCACert -file D:\BaltimoreCyberTrustRoot.crt.pem -keystore truststore -storepass password -noprompt
    ```

    ```console
    keytool -importcert -alias MySQLServerCACert2 -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password -noprompt
    ```

    Em seguida, substitua o ficheiro original da loja de chaves pelo novo gerado:

    * System.setProperty ("javax.net.ssl.trustStore","path_to_truststore_file");
    * System.setProperty ("javax.net.ssl.trustStorePassword","password");

  * Para os utilizadores .NET (MySQL Connector/NET, MySQLConnector), certifique-se de que **BaltimoreCyberTrustRoot** e **DigiCertGlobalRootG2** existem ambos na Windows Certificate Store, Autoridades de Certificação de Raiz Fidedigna. Se não existirem certificados, importe o certificado em falta.

    :::image type="content" source="media/overview/netconnecter-cert.png" alt-text="Azure Database para MySQL .net cert diagrama":::

  * Para os utilizadores de .NET no Linux que utilizam SSL_CERT_DIR, certifique-se de que **baltimoreCyberTrustRoot** e **DigiCertGlobalRootG2** existem no diretório indicado por SSL_CERT_DIR. Se não existirem certificados, crie o ficheiro de certificado em falta.

  * Para outros utilizadores (MySQL Client/MySQL Workbench/C/C++/Go/Python/Ruby/PHP/NodeJS/Perl/Swift), pode fundir dois ficheiros de certificados CA no seguinte formato:

      ```
      -----BEGIN CERTIFICATE-----
      (Root CA1: BaltimoreCyberTrustRoot.crt.pem)
      -----END CERTIFICATE-----
      -----BEGIN CERTIFICATE-----
      (Root CA2: DigiCertGlobalRootG2.crt.pem)
      -----END CERTIFICATE-----
      ```

* Substitua o ficheiro ca pem de raiz original pelo ficheiro CA raiz combinado e reinicie a sua aplicação/cliente.
* No futuro, após o novo certificado implantado no lado do servidor, pode alterar o seu ficheiro CA pem para DigiCertGlobalRootG2.crt.pem.

> [!NOTE]
> Por favor, não largue ou **altere** o certificado de Baltimore até que a alteração do certificado seja feita. Enviaremos uma comunicação assim que a mudança estiver feita, após a qual é seguro que eles larguem o certificado de Baltimore. 

## <a name="why-was-baltimorecybertrustroot-certificate-not-replaced-to-digicertglobalrootg2-during-this-change-on-february-15-2021"></a>Porque é que o certificado BaltimoreCyberTrustRoot não foi substituído pela DigiCertGlobalRootG2 durante esta mudança em 15 de fevereiro de 2021?

Avaliámos a prontidão do cliente para esta mudança e percebemos que muitos clientes estavam à procura de um prazo adicional para gerir esta mudança. Com o intuição de proporcionar mais tempo de antecedência aos clientes para a prontidão, decidimos adiar a alteração do certificado para DigiCertGlobalRootG2 por pelo menos um ano, proporcionando tempo suficiente de antecedência aos clientes e utilizadores finais. 

As nossas recomendações para os utilizadores são: use as medidas acima mencionadas para criar um certificado combinado e ligar-se ao seu servidor, mas não remova o certificado BaltimoreCyberTrustRoot até enviarmos uma comunicação para removê-lo. 

## <a name="what-if-we-removed-the-baltimorecybertrustroot-certificate"></a>E se retirarmos o certificado BaltimoreCyberTrustRoot?

Começará a ter erros de conectividade ao ligar-se à base de dados Azure para o servidor MySQL. Terá de [configurar o certificado SSL](howto-configure-ssl.md) com [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) novamente para manter a conectividade.


## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="1-if-im-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1. Se não estiver a usar SSL/TLS, ainda preciso de atualizar a raiz ca?

  Não são necessárias ações se não estiver a utilizar o SSL/TLS.

### <a name="2-if-im-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2. Se estiver a usar SSL/TLS, preciso de reiniciar o meu servidor de base de dados para atualizar o CA raiz?

Não, não precisa reiniciar o servidor de base de dados para começar a usar o novo certificado. Este certificado de raiz é uma alteração do lado do cliente e as ligações do cliente que chegam precisam de usar o novo certificado para garantir que podem ligar-se ao servidor de base de dados.

### <a name="3-how-do-i-know-if-im-using-ssltls-with-root-certificate-verification"></a>3. Como posso saber se estou a usar SSL/TLS com verificação de certificados de raiz?

Pode identificar se as suas ligações verificam o certificado raiz, revendo a sua cadeia de ligação.

- Se a sua cadeia de ligação incluir `sslmode=verify-ca` `sslmode=verify-identity` ou, tem de atualizar o certificado.
- Se a sua cadeia de ligação incluir `sslmode=disable` , ou , não precisa atualizar `sslmode=allow` `sslmode=prefer` `sslmode=require` certificados.
- Se a sua cadeia de ligação não especificar sslmode, não precisa de atualizar certificados.

Se estiver a usar um cliente que abstrate a cadeia de ligação, reveja a documentação do cliente para perceber se verifica os certificados.

### <a name="4-what-is-the-impact-if-using-app-service-with-azure-database-for-mysql"></a>4. Qual é o impacto se utilizar o Serviço de Aplicações com Base de Dados Azure para o MySQL?

Para os serviços de aplicações Azure que ligam a Base de Dados Azure para o MySQL, existem dois cenários possíveis e dependendo de como você está usando SSL com a sua aplicação.

* Este novo certificado foi adicionado ao Serviço de Aplicações a nível da plataforma. Se estiver a utilizar os certificados SSL incluídos na plataforma do Serviço de Aplicações na sua aplicação, então não é necessária qualquer ação. Este é o cenário mais comum. 
* Se estiver explicitamente a incluir o caminho para o ficheiro SSL cert no seu código, então terá de descarregar o novo certificado e produzir um certificado combinado como mencionado acima e utilizar o ficheiro de certificado. Um bom exemplo deste cenário é quando utiliza recipientes personalizados no Serviço de Aplicações, como partilhado na documentação do [Serviço de Aplicações.](../app-service/tutorial-multi-container-app.md#configure-database-variables-in-wordpress) Este é um cenário incomum, mas temos visto alguns utilizadores a usar isto.

### <a name="5-what-is-the-impact-if-using-azure-kubernetes-services-aks-with-azure-database-for-mysql"></a>5. Qual é o impacto se utilizar os Serviços Azure Kubernetes (AKS) com a Base de Dados Azure para o MySQL?

Se está a tentar ligar-se à Base de Dados Azure para o MySQL utilizando os Serviços Azure Kubernetes (AKS), é semelhante ao acesso a partir de um ambiente de hospedagem dedicado aos clientes. Consulte os passos [aqui.](../aks/ingress-own-tls.md)

### <a name="6-what-is-the-impact-if-using-azure-data-factory-to-connect-to-azure-database-for-mysql"></a>6. Qual é o impacto se utilizar a Azure Data Factory para ligar à Base de Dados Azure para o MySQL?

Para um conector que utiliza o Tempo de Execução da Integração Azure, os certificados de alavancagem do conector na Loja de Certificados do Windows no ambiente hospedado no Azure. Estes certificados já são compatíveis com os certificados recentemente aplicados, pelo que não é necessária qualquer ação.

Para um conector que utilize o Tempo de Execução de Integração Auto-hospedado, onde inclui explicitamente o caminho para o ficheiro cert SSL na sua cadeia de ligação, terá de descarregar o [novo certificado](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) e atualizar o fio de ligação para o utilizar.

### <a name="7-do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>7. Preciso de planear um tempo de inatividade de manutenção do servidor de bases de dados para esta mudança?

N.º Uma vez que a mudança aqui é apenas do lado do cliente para se ligar ao servidor de base de dados, não há tempo de inatividade de manutenção necessário para o servidor de base de dados para esta alteração.

### <a name="8-if-i-create-a-new-server-after-february-15-2021-02152021-will-i-be-impacted"></a>8. Se eu criar um novo servidor depois de 15 de fevereiro de 2021 (02/15/2021), serei impactado?

Para servidores criados após 15 de fevereiro de 2021 (02/15/2021), continuará a utilizar o [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) para as suas aplicações ligarem-se através do SSL.

### <a name="9-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>9. Com que frequência a Microsoft atualiza os seus certificados ou qual é a política de caducidade?

Estes certificados utilizados pela Azure Database for MySQL são fornecidos pelas Autoridades de Certificados Fidedignos (CA). Assim, o apoio destes certificados está ligado ao apoio destes certificados pela AC. O certificado [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) está previsto expirar em 2025, pelo que a Microsoft terá de efetuar uma alteração de certificado antes do termo. Também no caso de existirem bugs imprevistos nestes certificados predefinidos, a Microsoft terá de fazer a rotação do certificado o mais cedo possível, semelhante à alteração realizada a 15 de fevereiro de 2021 para garantir que o serviço está seguro e em conformidade em todos os momentos.

### <a name="10-if-im-using-read-replicas-do-i-need-to-perform-this-update-only-on-source-server-or-the-read-replicas"></a>10. Se estiver a usar réplicas de leitura, preciso de realizar esta atualização apenas no servidor de origem ou nas réplicas de leitura?

Uma vez que esta atualização é uma alteração do lado do cliente, se o cliente usou para ler dados a partir do servidor de réplica, também terá de aplicar as alterações para esses clientes.

### <a name="11-if-im-using-data-in-replication-do-i-need-to-perform-any-action"></a>11. Se estou a usar a replicação do Data-in, preciso de fazer alguma ação?

Se estiver a utilizar a [replicação de dados](concepts-data-in-replication.md) para ligar à Base de Dados Azure para o MySQL, há duas coisas a considerar:

* Se a replicação de dados for de uma máquina virtual (on-prem ou azure virtual machine) para Azure Database for MySQL, é necessário verificar se o SSL está a ser utilizado para criar a réplica. Executar **SHOW SLAVE STATUS** e verificar a seguinte definição.  

    ```azurecli-interactive
    Master_SSL_Allowed            : Yes
    Master_SSL_CA_File            : ~\azure_mysqlservice.pem
    Master_SSL_CA_Path            :
    Master_SSL_Cert               : ~\azure_mysqlclient_cert.pem
    Master_SSL_Cipher             :
    Master_SSL_Key                : ~\azure_mysqlclient_key.pem
    ```

    Se vir que o certificado está fornecido para o CA_file, SSL_Cert e SSL_Key, terá de atualizar o ficheiro adicionando o [novo certificado](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) e criar um ficheiro cert combinado.

* Se a replicação de dados estiver entre duas Bases de Dados Azure para o MySQL, então terá de redefinir a réplica executando **call mysql.az_replication_change_master** e fornecer o novo certificado de raiz dupla como último parâmetro [master_ssl_ca](howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication)

### <a name="12-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>12. Temos uma consulta do lado do servidor para verificar se o SSL está a ser utilizado?

Para verificar se está a utilizar a ligação SSL para ligar ao servidor, consulte a [verificação SSL](howto-configure-ssl.md#step-4-verify-the-ssl-connection).

### <a name="13-is-there-an-action-needed-if-i-already-have-the-digicertglobalrootg2-in-my-certificate-file"></a>13. É necessária alguma ação se já tiver o DigiCertGlobalRootG2 no meu ficheiro de certificado?

N.º Não é necessária nenhuma ação se o seu ficheiro de certificado já tiver o **DigiCertGlobalRootG2**.

### <a name="14-what-if-i-have-further-questions"></a>14. E se eu tiver mais perguntas?

Se tiver dúvidas, obtenha respostas de especialistas comunitários no [Microsoft Q&A](mailto:AzureDatabaseforMySQL@service.microsoft.com). Se tiver um plano de apoio e precisar de ajuda técnica, [contacte-nos](mailto:AzureDatabaseforMySQL@service.microsoft.com).
