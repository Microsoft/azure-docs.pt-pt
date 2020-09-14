---
title: Rotação de certificado para Base de Dados Azure para MariaDB
description: Conheça as próximas alterações das alterações ao certificado de raiz que afetarão a Base de Dados do Azure para a MariaDB
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: ba5a1a0eb61ca086c3cd1ea66acddc18e950871d
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2020
ms.locfileid: "90057247"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-mariadb"></a>Compreender as alterações na alteração do CA raiz para a Base de Dados Azure para MariaDB

A Azure Database for MariaDB irá alterar o certificado de raiz para a aplicação/controlador do cliente ativado com SSL, utilizar para [ligar ao servidor de base de dados](concepts-connectivity-architecture.md). O certificado raiz atualmente disponível está previsto para expirar a 26 de outubro de 2020 (10/26/2020) como parte das melhores práticas de manutenção e segurança. Este artigo dá-lhe mais detalhes sobre as próximas alterações, os recursos que serão afetados e os passos necessários para garantir que a sua aplicação mantém a conectividade com o servidor da sua base de dados.

## <a name="what-update-is-going-to-happen"></a>Que atualização vai acontecer?

Em alguns casos, os pedidos utilizam um ficheiro de certificado local gerado a partir de um ficheiro de certificado fidedigno da Autoridade de Certificados (CA) para se conectarem de forma segura. Atualmente, os clientes só podem usar o certificado predefinido para ligar a uma Base de Dados Azure para o servidor MariaDB, que está localizado [aqui.](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) No entanto, [o Fórum de Navegador da Autoridade de Certificados (CA)](https://cabforum.org/)publicou   recentemente relatórios de vários certificados emitidos por fornecedores de CA para não cumprirem.

De acordo com os requisitos de conformidade da indústria, os fornecedores de CA começaram a revogar os certificados de CA para CAs não conformes, exigindo que os servidores usassem certificados emitidos por AA em conformidade, e assinados por certificados ca desses CAs em conformidade. Uma vez que a Azure Database for MariaDB utiliza atualmente um destes certificados não conformes, que as aplicações do cliente utilizam para validar as suas ligações SSL, precisamos de garantir que sejam tomadas medidas adequadas (descritas abaixo) para minimizar o impacto potencial nos seus servidores MariaDB.


O novo certificado será usado a partir de 26 de outubro de 2020 (10/26/2020). Se utilizar a validação ca ou a validação completa do certificado do servidor ao ligar-se a um cliente MySQL (sslmode=check-ca ou sslmode=check-full), tem de atualizar a configuração da sua aplicação antes de 26 de outubro de 2020 (10/26/2020).

## <a name="how-do-i-know-if-my-database-is-going-to-be-affected"></a>Como sei se a minha base de dados vai ser afetada?

Todas as aplicações que utilizam SSL/TLS e verificam o certificado raiz precisam de atualizar o certificado raiz para se ligar à Base de Dados Azure para MariaDB. Se não estiver a utilizar o SSL/TLS atualmente, não há qualquer impacto na disponibilidade da sua aplicação. Pode verificar se a sua aplicação de cliente está a tentar utilizar o modo SSL com a Autoridade de Certificados de Confiança predefinida (CA) [aqui](concepts-ssl-connection-security.md#default-settings).

Para evitar que a disponibilidade da sua candidatura seja interrompida devido à revogação inesperada dos certificados, ou à atualização de um certificado, que foi revogado, consulte a secção [**"O que preciso de fazer para manter a conectividade".**](concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity)

## <a name="what-do-i-need-to-do-to-maintain-connectivity"></a>O que preciso fazer para manter a conectividade

Para evitar que a disponibilidade da sua candidatura seja interrompida devido à revogação inesperada dos certificados, ou à atualização de um certificado, que foi revogado, siga os passos abaixo:

*   Baixe **BaltimoreCyberTrustRoot**  &  **DigiCertGlobalRootG2** CA a partir de links abaixo:
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   Geram uma loja de certificados CA combinada com certificados **BaltimoreCyberTrustRoot** e **DigiCertGlobalRootG2.**
    *   Para os utilizadores java (Ligação MariaDB/J), execute:

          ```azurecli-interactive
          keytool -importcert -alias MariaDBServerCACert  -file D:\BaltimoreCyberTrustRoot.crt.pem  -keystore truststore -storepass password -noprompt
          ```

          ```azurecli-interactive
          keytool -importcert -alias MariaDBServerCACert2  -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password  -noprompt
          ```

          Em seguida, substitua o ficheiro original da loja de chaves pelo novo gerado:
        *   System.setProperty ("javax.net.ssl.trustStore","path_to_truststore_file"); 
        *   System.setProperty ("javax.net.ssl.trustStorePassword","password");
    *   Para os utilizadores .NET (MariaDB Connector/NET, MariaDBConnector), certifique-se de que **BaltimoreCyberTrustRoot** e **DigiCertGlobalRootG2** existem ambos na Windows Certificate Store, Autoridades de Certificação de Raiz Fidedigna. Se não existirem certificados, importe o certificado em falta.

        ![Base de Dados Azure para MariaDB .net cert](media/overview/netconnecter-cert.png)

    *   Para os utilizadores de .NET no Linux que utilizam SSL_CERT_DIR, certifique-se de que **baltimoreCyberTrustRoot** e **DigiCertGlobalRootG2** existem no diretório indicado por SSL_CERT_DIR. Se não existirem certificados, crie o ficheiro de certificado em falta.

    *   Para outros utilizadores (MariaDB Client/MariaDB Workbench/C/C++/Go/Python/Ruby/PHP/NodeJS/Perl/Swift), pode fundir dois ficheiros de certificados CA como este formato abaixo</b>

        </br>-----BEGIN CERTIFICADO-----
 </br>(Raiz CA1: BaltimoreCyberTrustRoot.crt.pem)
 </br>-----END CERTIFICATE-----
 </br>-----BEGIN CERTIFICADO-----
 </br>(Raiz CA2: DigiCertGlobalRootG2.crt.pem)
 </br>-----END CERTIFICATE-----

*   Substitua o ficheiro ca pem de raiz original pelo ficheiro CA raiz combinado e reinicie a sua aplicação/cliente.
*   No futuro, após o novo certificado implantado no lado do servidor, pode alterar o seu ficheiro CA pem para DigiCertGlobalRootG2.crt.pem.

## <a name="what-can-be-the-impact"></a>Qual pode ser o impacto?
Se estiver a utilizar o certificado emitido pela Base de Dados Azure para MariaDB, como documentado aqui, a disponibilidade da sua candidatura poderá ser interrompida uma vez que a base de dados não será alcançável. Dependendo da sua aplicação, poderá receber uma variedade de mensagens de erro, incluindo, mas não se limitando a:
*   Certificado inválido/certificado revogado
*   Excedido o limite de tempo da ligação
*   Erro, se aplicável

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="1-if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1. Se não estiver a utilizar sSL/TLS, ainda preciso de atualizar a raiz ca?
Não são necessárias ações se não estiver a utilizar o SSL/TLS. 

### <a name="2-if-i-am-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2. Se estiver a utilizar o SSL/TLS, preciso de reiniciar o meu servidor de base de dados para atualizar o CA raiz?
Não, não precisa de reiniciar o servidor de base de dados para começar a usar o novo certificado. Trata-se de uma alteração do lado do cliente e as ligações ao cliente que chegam precisam de utilizar o novo certificado para garantir que podem ligar-se ao servidor de base de dados.

### <a name="3-what-will-happen-if-i-do-not-update-the-root-certificate-before-october-26-2020-10262020"></a>3. O que acontecerá se eu não atualizar o certificado de raiz antes de 26 de outubro de 2020 (10/26/2020)?
Se não atualizar o certificado de raiz antes de 30 de novembro de 2020, as suas aplicações que se ligam via SSL/TLS e que faz a verificação do certificado raiz não poderão comunicar ao servidor de base de dados MariaDB e a aplicação experimentará problemas de conectividade com o seu servidor de base de dados MariaDB.

### <a name="4-do-i-need-to-plan-a-maintenance-downtime-for-this-changebr"></a>4. Preciso de planear um tempo de paragem de manutenção para esta mudança?<BR>
Não. Uma vez que a mudança aqui é apenas do lado do cliente para ligar ao servidor de base de dados, não há tempo de inatividade de manutenção aqui para esta mudança.

### <a name="5--what-if-i-cannot-get-a-scheduled-downtime-for-this-change-before-october-26-2020-10262020"></a>5. E se eu não conseguir um tempo de paragem programado para esta mudança antes de 26 de outubro de 2020 (10/26/2020)?
Uma vez que os clientes utilizados para a ligação ao servidor precisam de estar a atualizar as informações do certificado tal como descrito na secção de correção [aqui,](./concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity)não precisamos de um tempo de inatividade para o servidor neste caso.

###  <a name="6-if-i-create-a-new-server-after-nov-30th-will-i-be-impacted"></a>6. Se eu criar um novo servidor depois de 30 de novembro, serei impactado?
Para servidores criados após 26 de outubro de 2020 (10/26/2020), pode utilizar o certificado recém-emitido para as suas aplicações para se conectar através do SSL.

### <a name="7-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>7. Com que frequência a Microsoft atualiza os seus certificados ou qual é a política de caducidade?
Estes certificados utilizados pela Base de Dados Azure para MariaDB são fornecidos pelas Autoridades de Certificados Fidedignos (CA). Assim, o apoio destes certificados na Base de Dados Azure para MariaDB está ligado ao apoio destes certificados pela AC. No entanto, como neste caso, podem existir bugs imprevistos nestes certificados predefinidos, que precisam de ser corrigidos o mais cedo possível.

### <a name="8-if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-master-server-or-all-the-read-replicas"></a>8. Se estiver a usar réplicas de leitura, preciso de realizar esta atualização apenas no servidor principal ou em todas as réplicas lidas?
Uma vez que esta atualização é uma alteração do lado do cliente, se o cliente usou para ler dados a partir do servidor de réplica, também teremos de aplicar as alterações para esses clientes. 

### <a name="9-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>9. Temos uma consulta do lado do servidor para verificar se o SSL está a ser utilizado?
Para verificar se está a utilizar a ligação SSL para ligar ao servidor, consulte a [verificação SSL](howto-configure-ssl.md#verify-the-ssl-connection).

### <a name="10-what-if-i-have-further-questions"></a>10. E se eu tiver mais perguntas?
Se tiver dúvidas, obtenha respostas de especialistas comunitários no [Microsoft Q&A](mailto:AzureDatabaseformariadb@service.microsoft.com). Se tiver um plano de apoio e precisar de ajuda técnica, [contacte-nos](mailto:AzureDatabaseformariadb@service.microsoft.com)
