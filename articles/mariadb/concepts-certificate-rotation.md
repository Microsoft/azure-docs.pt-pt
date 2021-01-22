---
title: Rotação de certificado para Base de Dados Azure para MariaDB
description: Conheça as próximas alterações das alterações ao certificado de raiz que afetarão a Base de Dados do Azure para a MariaDB
author: mksuni
ms.author: sumuth
ms.service: jroth
ms.topic: conceptual
ms.date: 01/18/2021
ms.openlocfilehash: 66db443c4c52e4994e62a9f83f8a624319b349ab
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98659891"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-mariadb"></a>Compreender as alterações na alteração do CA raiz para a Base de Dados Azure para MariaDB

A Azure Database for MariaDB irá alterar o certificado de raiz para a aplicação/controlador do cliente ativado com SSL, utilizar para [ligar ao servidor de base de dados](concepts-connectivity-architecture.md). O certificado raiz atualmente disponível está previsto para expirar a 15 de fevereiro de 2021 (02/15/2021) como parte das melhores práticas de manutenção e segurança. Este artigo dá-lhe mais detalhes sobre as próximas alterações, os recursos que serão afetados e os passos necessários para garantir que a sua aplicação mantém a conectividade com o servidor da sua base de dados.

>[!NOTE]
> Com base no feedback dos clientes, alargamos a depreciação do certificado de raiz para a nossa ca raiz de Baltimore existente de 26 de outubro de 2020 até 15 de fevereiro de 2021. Esperamos que esta extensão proporcione tempo suficiente para que os nossos utilizadores implementem as alterações do cliente se forem impactadas.

> [!NOTE]
> Este artigo contém referências ao termo _escravo_, um termo que a Microsoft já não utiliza. Quando o termo for removido do software, vamos removê-lo deste artigo.

## <a name="what-update-is-going-to-happen"></a>Que atualização vai acontecer?

Em alguns casos, os pedidos utilizam um ficheiro de certificado local gerado a partir de um ficheiro de certificado fidedigno da Autoridade de Certificados (CA) para se conectarem de forma segura. Atualmente, os clientes só podem usar o certificado predefinido para ligar a uma Base de Dados Azure para o servidor MariaDB, que está localizado [aqui.](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) No entanto, [o Fórum de Navegador da Autoridade de Certificados (CA)](https://cabforum.org/)publicou   recentemente relatórios de vários certificados emitidos por fornecedores de CA para não cumprirem.

De acordo com os requisitos de conformidade da indústria, os fornecedores de CA começaram a revogar os certificados de CA para CAs não conformes, exigindo que os servidores usassem certificados emitidos por AA em conformidade, e assinados por certificados ca desses CAs em conformidade. Uma vez que a Azure Database for MariaDB utiliza atualmente um destes certificados não conformes, que as aplicações do cliente utilizam para validar as suas ligações SSL, precisamos de garantir que sejam tomadas medidas adequadas (descritas abaixo) para minimizar o impacto potencial nos seus servidores MariaDB.

O novo certificado será usado a partir de 15 de fevereiro de 2021 (02/15/2021). Se utilizar a validação ca ou a validação completa do certificado do servidor ao ligar-se a um cliente MySQL (sslmode=check-ca ou sslmode=check-full), tem de atualizar a configuração da sua aplicação antes de 15 de fevereiro de 2021 (02/15/2021).

## <a name="how-do-i-know-if-my-database-is-going-to-be-affected"></a>Como sei se a minha base de dados vai ser afetada?

Todas as aplicações que utilizam SSL/TLS e verificam o certificado raiz precisam de atualizar o certificado raiz. Pode identificar se as suas ligações verificam o certificado raiz, revendo a sua cadeia de ligação.

- Se a sua cadeia de ligação incluir `sslmode=verify-ca` `sslmode=verify-identity` ou, tem de atualizar o certificado.
- Se a sua cadeia de ligação incluir `sslmode=disable` , ou , não precisa atualizar `sslmode=allow` `sslmode=prefer` `sslmode=require` certificados.
- Se a sua cadeia de ligação não especificar sslmode, não precisa de atualizar certificados.

Se estiver a usar um cliente que abstrate a cadeia de ligação, reveja a documentação do cliente para perceber se verifica os certificados.
Para compreender a Base de Dados Azure para sslmode MariaDB, reveja as descrições do [modo SSL](concepts-ssl-connection-security.md#default-settings).

Para evitar que a disponibilidade da sua candidatura seja interrompida em resultado da revogação inesperada dos certificados, ou de atualizar um certificado, que foi revogado, consulte a secção [**"O que preciso de fazer para manter a conectividade".**](concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity)

## <a name="what-do-i-need-to-do-to-maintain-connectivity"></a>O que preciso fazer para manter a conectividade

Para evitar que a disponibilidade da sua candidatura seja interrompida devido à revogação inesperada dos certificados, ou à atualização de um certificado, que foi revogado, siga os passos abaixo. A ideia é criar um novo ficheiro *.pem,* que combine o cert atual e o novo e durante a validação do cert SSL após a utilização dos valores permitidos. Consulte os passos abaixo:

- Baixe **BaltimoreCyberTrustRoot**  &  **DigiCertGlobalRootG2** CA a partir de links abaixo:

  - [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)
  - [https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)

- Geram uma loja de certificados CA combinada com certificados **BaltimoreCyberTrustRoot** e **DigiCertGlobalRootG2.**

  - Para os utilizadores java (Ligação MariaDB/J), execute:

    ```console
    keytool -importcert -alias MariaDBServerCACert  -file D:\BaltimoreCyberTrustRoot.crt.pem  -keystore truststore -storepass password -noprompt
    ```

    ```console
    keytool -importcert -alias MariaDBServerCACert2  -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password  -noprompt
    ```

    Em seguida, substitua o ficheiro original da loja de chaves pelo novo gerado:

    - System.setProperty ("javax.net.ssl.trustStore","path_to_truststore_file");
    - System.setProperty ("javax.net.ssl.trustStorePassword","password");

  - Para os utilizadores .NET (MariaDB Connector/NET, MariaDBConnector), certifique-se de que **BaltimoreCyberTrustRoot** e **DigiCertGlobalRootG2** existem ambos na Windows Certificate Store, Autoridades de Certificação de Raiz Fidedigna. Se não existirem certificados, importe o certificado em falta.

    [![Base de Dados Azure para MariaDB .net cert](media/overview/netconnecter-cert.png)](media/overview/netconnecter-cert.png#lightbox)

  - Para os utilizadores de .NET no Linux que utilizam SSL_CERT_DIR, certifique-se de que **baltimoreCyberTrustRoot** e **DigiCertGlobalRootG2** existem no diretório indicado por SSL_CERT_DIR. Se não existirem certificados, crie o ficheiro de certificado em falta.

  - Para outros utilizadores (MariaDB Client/MariaDB Workbench/C/C++/Go/Python/Ruby/PHP/NodeJS/Perl/Swift), pode fundir dois ficheiros de certificados CA como este formato abaixo

   ```
   -----BEGIN CERTIFICATE-----
   (Root CA1: BaltimoreCyberTrustRoot.crt.pem)
   -----END CERTIFICATE-----
   -----BEGIN CERTIFICATE-----
   (Root CA2: DigiCertGlobalRootG2.crt.pem)
   -----END CERTIFICATE-----
   ```

- Substitua o ficheiro ca pem de raiz original pelo ficheiro CA raiz combinado e reinicie a sua aplicação/cliente.
- No futuro, após o novo certificado implantado no lado do servidor, pode alterar o seu ficheiro CA pem para DigiCertGlobalRootG2.crt.pem.

## <a name="what-can-be-the-impact-of-not-updating-the-certificate"></a>Qual pode ser o impacto de não atualizar o certificado?

Se estiver a utilizar o certificado emitido pela Base de Dados Azure para MariaDB, conforme documentado aqui, a disponibilidade da sua candidatura poderá ser interrompida uma vez que a base de dados não será alcançável. Dependendo da sua aplicação, poderá receber várias mensagens de erro, incluindo, mas não se limitando a:

- Certificado inválido/certificado revogado
- Excedido o limite de tempo da ligação

> [!NOTE]
> Por favor, não largue ou **altere** o certificado de Baltimore até que a alteração do certificado seja feita. Enviaremos uma comunicação depois da mudança, após a qual é seguro que eles larguem o certificado de Baltimore.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="1-if-im-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1. Se não estiver a usar SSL/TLS, ainda preciso de atualizar a raiz ca?

Não são necessárias ações se não estiver a utilizar SSL/TLS.

### <a name="2-if-im-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2. Se estiver a usar SSL/TLS, preciso de reiniciar o meu servidor de base de dados para atualizar o CA raiz?

Não, não precisa reiniciar o servidor de base de dados para começar a usar o novo certificado. A atualização do certificado é uma alteração do lado do cliente, e as ligações do cliente que chegam precisam de usar o novo certificado para garantir que podem ligar-se ao servidor de base de dados.

### <a name="3-what-will-happen-if-i-dont-update-the-root-certificate-before-february-15-2021-02152021"></a>3. O que acontecerá se eu não atualizar o certificado de raiz antes de 15 de fevereiro de 2021 (02/15/2021)?

Se não atualizar o certificado de raiz antes de 15 de fevereiro de 2021 (02/15/2021), as suas aplicações que se ligam via SSL/TLS e fazem a verificação do certificado raiz não poderão comunicar ao servidor de base de dados MariaDB e a aplicação experimentará problemas de conectividade no seu servidor de base de dados MariaDB.

### <a name="4-what-is-the-impact-if-using-app-service-with-azure-database-for-mariadb"></a>4. Qual é o impacto se utilizar o Serviço de Aplicações com Base de Dados Azure para a MariaDB?

Para os serviços de aplicações Azure, conectando-se à Base de Dados Azure para MariaDB, existem dois cenários possíveis dependendo de como você está usando SSL com a sua aplicação.

- Este novo certificado foi adicionado ao Serviço de Aplicações a nível da plataforma. Se estiver a utilizar os certificados SSL incluídos na plataforma do Serviço de Aplicações na sua aplicação, então não é necessária qualquer ação.
- Se estiver explicitamente a incluir o caminho para o ficheiro SSL cert no seu código, então terá de descarregar o novo certificado e atualizar o código para utilizar o novo certificado. Um bom exemplo deste cenário é quando utiliza recipientes personalizados no Serviço de Aplicações como partilhado na documentação do [Serviço de Aplicações](../app-service/tutorial-multi-container-app.md#configure-database-variables-in-wordpress)

### <a name="5-what-is-the-impact-if-using-azure-kubernetes-services-aks-with-azure-database-for-mariadb"></a>5. Qual é o impacto se utilizar os Serviços Azure Kubernetes (AKS) com a Base de Dados Azure para a MariaDB?

Se está a tentar ligar-se à Base de Dados Azure para MariaDB utilizando os Serviços Azure Kubernetes (AKS), é semelhante ao acesso a partir de um ambiente de hospedagem dedicado aos clientes. Consulte os passos [aqui.](../aks/ingress-own-tls.md)

### <a name="6-what-is-the-impact-if-using-azure-data-factory-to-connect-to-azure-database-for-mariadb"></a>6. Qual é o impacto se utilizar a Azure Data Factory para ligar à Base de Dados Azure para a MariaDB?

Para o conector que utiliza o Tempo de Execução da Integração Azure, o conector utiliza certificados na Loja de Certificados do Windows no ambiente hospedado no Azure. Estes certificados já são compatíveis com os certificados recentemente aplicados, pelo que não é necessária qualquer ação.

Para o conector que utiliza o Tempo de Execução de Integração Auto-hospedado, onde inclui explicitamente o caminho para o ficheiro cert SSL na sua cadeia de ligação, terá de descarregar o [novo certificado](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) e atualizar o fio de ligação para o utilizar.

### <a name="7-do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>7. Preciso de planear um tempo de inatividade de manutenção do servidor de bases de dados para esta mudança?

N.º Uma vez que a mudança aqui é apenas do lado do cliente para ligar ao servidor de base de dados, não há tempo de inatividade de manutenção necessário para o servidor de base de dados para esta mudança.

### <a name="8--what-if-i-cant-get-a-scheduled-downtime-for-this-change-before-february-15-2021-02152021"></a>8. E se eu não conseguir um tempo de paragem programado para esta mudança antes de 15 de fevereiro de 2021 (02/15/2021)?

Uma vez que os clientes utilizados para a ligação ao servidor precisam de atualizar as informações do certificado tal como descrito na secção de correção [aqui,](./concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity)não precisamos de um tempo de inatividade para o servidor neste caso.

### <a name="9-if-i-create-a-new-server-after-february-15-2021-02152021-will-i-be-impacted"></a>9. Se eu criar um novo servidor depois de 15 de fevereiro de 2021 (02/15/2021), serei impactado?

Para servidores criados após 15 de fevereiro de 2021 (02/15/2021), pode utilizar o certificado recém-emitido para as suas aplicações para se conectar através do SSL.

### <a name="10-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>10. Com que frequência a Microsoft atualiza os seus certificados ou qual é a política de caducidade?

Estes certificados utilizados pela Base de Dados Azure para MariaDB são fornecidos pelas Autoridades de Certificados Fidedignos (CA). Assim, o apoio destes certificados na Base de Dados Azure para MariaDB está ligado ao apoio destes certificados pela AC. No entanto, como neste caso, podem existir bugs imprevistos nestes certificados predefinidos, que precisam de ser corrigidos o mais cedo possível.

### <a name="11-if-im-using-read-replicas-do-i-need-to-perform-this-update-only-on-source-server-or-the-read-replicas"></a>11. Se estiver a usar réplicas de leitura, preciso de realizar esta atualização apenas no servidor de origem ou nas réplicas de leitura?

Uma vez que esta atualização é uma alteração do lado do cliente, se o cliente usou para ler dados a partir do servidor de réplica, também terá de aplicar as alterações para esses clientes.

### <a name="12-if-im-using-data-in-replication-do-i-need-to-perform-any-action"></a>12. Se estou a usar a replicação do Data-in, preciso de fazer alguma ação?

- Se a replicação de dados for de uma máquina virtual (on-prem ou azure virtual machine) para Azure Database for MySQL, é necessário verificar se o SSL está a ser utilizado para criar a réplica. Executar **SHOW SLAVE STATUS** e verificar a seguinte definição.

    ```azurecli-interactive
    Master_SSL_Allowed            : Yes
    Master_SSL_CA_File            : ~\azure_mysqlservice.pem
    Master_SSL_CA_Path            :
    Master_SSL_Cert               : ~\azure_mysqlclient_cert.pem
    Master_SSL_Cipher             :
    Master_SSL_Key                : ~\azure_mysqlclient_key.pem
    ```

Se estiver a utilizar a [replicação de dados](concepts-data-in-replication.md) para ligar à Base de Dados Azure para o MySQL, há duas coisas a considerar:

- Se a replicação de dados for de uma máquina virtual (on-prem ou azure virtual machine) para Azure Database for MySQL, é necessário verificar se o SSL está a ser utilizado para criar a réplica. Executar **SHOW SLAVE STATUS** e verificar a seguinte definição. 

  ```azurecli-interactive
  Master_SSL_Allowed            : Yes
  Master_SSL_CA_File            : ~\azure_mysqlservice.pem
  Master_SSL_CA_Path            :
  Master_SSL_Cert               : ~\azure_mysqlclient_cert.pem
  Master_SSL_Cipher             :
  Master_SSL_Key                : ~\azure_mysqlclient_key.pem
  ```

  Se vir que o certificado está previsto para o CA_file, SSL_Cert e SSL_Key, terá de atualizar o ficheiro adicionando o [novo certificado](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem).

- Se a replicação de dados estiver entre duas Bases de Dados Azure para o MySQL, então terá de redefinir a réplica executando **call mysql.az_replication_change_master** e fornecer o novo certificado de raiz dupla como último parâmetro [master_ssl_ca](howto-data-in-replication.md#link-the-source-and-replica-servers-to-start-data-in-replication).

### <a name="13-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>13. Temos uma consulta do lado do servidor para verificar se o SSL está a ser utilizado?

Para verificar se está a utilizar a ligação SSL para ligar ao servidor, consulte a [verificação SSL](howto-configure-ssl.md#verify-the-ssl-connection).

### <a name="14-is-there-an-action-needed-if-i-already-have-the-digicertglobalrootg2-in-my-certificate-file"></a>14. É necessária alguma ação se já tiver o DigiCertGlobalRootG2 no meu ficheiro de certificado?

N.º Não é necessária nenhuma ação se o seu ficheiro de certificado já tiver o **DigiCertGlobalRootG2**.

### <a name="15-what-if-i-have-further-questions"></a>15. E se eu tiver mais perguntas?

Se tiver dúvidas, obtenha respostas de especialistas comunitários no [Microsoft Q&A](mailto:AzureDatabaseformariadb@service.microsoft.com). Se tiver um plano de apoio e precisar de ajuda técnica, [contacte-nos](mailto:AzureDatabaseformariadb@service.microsoft.com).
