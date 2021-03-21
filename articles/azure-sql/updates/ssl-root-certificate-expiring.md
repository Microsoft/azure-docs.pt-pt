---
title: Rotação de certificado para Azure SQL Database & SQL Gestd Instance
description: Conheça as próximas alterações das alterações dos certificados de raiz que afetarão a Base de Dados Azure SQL e a Azure SQL Gerenciada Instância
author: srdan-bozovic-msft
ms.author: srbozovi
ms.service: sql-db-mi
ms.subservice: service
ms.topic: conceptual
ms.date: 09/13/2020
ms.openlocfilehash: 96367b143711c4ec5f3f8d609f048c72c6fded16
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97590856"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-sql-database--sql-managed-instance"></a>Compreender as alterações na alteração root CA para Azure SQL Database & SQL Managed Instance

A Azure SQL Database & SQL Managed Instance estará a alterar o certificado de raiz para a aplicação/condutor do cliente ativado com sSL, utilizado para estabelecer uma ligação segura de TDS. O [atual certificado de raiz](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) está previsto expirar a 26 de outubro de 2020 como parte das melhores práticas de manutenção e segurança. Este artigo dá-lhe mais detalhes sobre as próximas alterações, os recursos que serão afetados e os passos necessários para garantir que a sua aplicação mantém a conectividade com o servidor da sua base de dados.

## <a name="what-update-is-going-to-happen"></a>Que atualização vai acontecer?

[O Fórum de Navegador da Autoridade de Certificados (CA)](https://cabforum.org/) publicou recentemente relatórios de vários certificados emitidos por fornecedores de CA para não cumprirem.

De acordo com os requisitos de conformidade da indústria, os fornecedores de CA começaram a revogar os certificados de CA para CAs não conformes, exigindo que os servidores usassem certificados emitidos por AA em conformidade, e assinados por certificados ca desses CAs em conformidade. Uma vez que a Azure SQL Database & SQL Managed Instance utilizam atualmente um destes certificados não conformes, que as aplicações do cliente utilizam para validar as suas ligações SSL, precisamos de garantir que sejam tomadas medidas adequadas (descritas abaixo) para minimizar o impacto potencial nos seus servidores Azure SQL.

O novo certificado será usado a partir de 26 de outubro de 2020. Se utilizar a validação completa do certificado do servidor ao ligar-se a um cliente SQL (TrustServerCertificate=true), tem de garantir que o seu cliente SQL poderá validar um novo certificado de raiz antes de 26 de outubro de 2020.

## <a name="how-do-i-know-if-my-application-might-be-affected"></a>Como sei se a minha candidatura pode ser afetada?

Todas as aplicações que utilizam O SSL/TLS e verificam o certificado raiz precisam de atualizar o certificado raiz para se ligar à Base de Dados Azure SQL & SQL Managed Instance. 

Se não estiver a utilizar o SSL/TLS atualmente, não há qualquer impacto na disponibilidade da sua aplicação. Pode verificar se o seu pedido de cliente está a tentar verificar o certificado de raiz olhando para a cadeia de ligação. Se o TrustServerCertificate estiver explicitamente definido como verdadeiro, então não será afetado.

Se o seu cliente utilizar a loja de certificados OS, como a maioria dos motoristas faz, e o seu SO for mantido regularmente, esta alteração provavelmente não o afetará, uma vez que o certificado de raiz para o qual estamos a mudar já deve estar disponível na sua Loja de Certificados de Raiz Fidedigna. Verifique se baltimore CyberDigiCert GlobalRoot G2 e valide-o presente.

Se o seu cliente utilizar a loja de certificados de arquivo local, para evitar que a disponibilidade da sua aplicação seja interrompida devido à revogação inesperada de certificados, ou para atualizar um certificado, que foi revogado, consulte o Que preciso de fazer para manter a secção [**de conectividade.**](./ssl-root-certificate-expiring.md#what-do-i-need-to-do-to-maintain-connectivity)

## <a name="what-do-i-need-to-do-to-maintain-connectivity"></a>O que preciso fazer para manter a conectividade

Para evitar que a disponibilidade da sua candidatura seja interrompida devido à revogação inesperada dos certificados, ou à atualização de um certificado, que foi revogado, siga os passos abaixo:

*   Baixe Baltimore CyberTrust Root & DigiCert GlobalRoot G2 Root CA a partir de links abaixo:
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   Geram uma loja de certificados CA combinada com certificados **BaltimoreCyberTrustRoot** e **DigiCertGlobalRootG2.**

## <a name="what-can-be-the-impact"></a>Qual pode ser o impacto?
Se estiver a validar certificados de servidor como documentado aqui, a disponibilidade da sua aplicação poderá ser interrompida uma vez que a base de dados não será alcançável. Dependendo da sua aplicação, poderá receber uma variedade de mensagens de erro, incluindo, mas não se limitando a:
*   Certificado inválido/certificado revogado
*   Excedido o limite de tempo da ligação
*   Erro, se aplicável

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>Se não estiver a utilizar sSL/TLS, ainda preciso de atualizar a raiz ca?
Não são necessárias ações relativas a esta alteração se não estiver a utilizar o SSL/TLS. Ainda assim, deverá definir um plano para começar a usar a versão mais recente do TLS, como planeamos para a aplicação do TLS num futuro próximo.

### <a name="what-will-happen-if-i-do-not-update-the-root-certificate-before-october-26-2020"></a>O que acontecerá se eu não atualizar o certificado de raiz antes de 26 de outubro de 2020?
Se não atualizar o certificado de raiz antes de 30 de novembro de 2020, as suas aplicações que se ligam via SSL/TLS e que faz a verificação do certificado raiz não poderão comunicar à Base de Dados Azure SQL & SQL Gestd Instance e aplicação experimentarão problemas de conectividade na sua Base de Dados Azure SQL & SQL Managed Instance.

### <a name="do-i-need-to-plan-a-maintenance-downtime-for-this-changebr"></a>Preciso de planear uma paragem de manutenção para esta mudança?<BR>
N.º Uma vez que a mudança aqui é apenas do lado do cliente para ligar ao servidor, não há tempo de inatividade de manutenção aqui para esta mudança.

### <a name="what-if-i-cannot-get-a-scheduled-downtime-for-this-change-before-october-26-2020"></a>E se eu não conseguir um tempo de paragem programado para esta mudança antes de 26 de outubro de 2020?
Uma vez que os clientes utilizados para a ligação ao servidor precisam de estar a atualizar as informações do certificado tal como descrito na secção de correção [aqui,](./ssl-root-certificate-expiring.md#what-do-i-need-to-do-to-maintain-connectivity)não precisamos de um tempo de inatividade para o servidor neste caso.

### <a name="if-i-create-a-new-server-after-november-30-2020-will-i-be-impacted"></a>Se eu criar um novo servidor depois de 30 de novembro de 2020, serei impactado?
Para os servidores criados depois de 26 de outubro de 2020, pode utilizar o certificado recém-emitido para as suas aplicações para se conectarem através do SSL.

### <a name="how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>Com que frequência a Microsoft atualiza os seus certificados ou qual é a política de caducidade?
Estes certificados utilizados pela Azure SQL Database & SQL Managed Instance são fornecidos pelas Autoridades de Certificados Fidedignos (CA). Assim, o suporte destes certificados na Base de Dados Azure SQL & SQL Managed Instance está ligado ao suporte destes certificados pela CA. No entanto, como neste caso, podem existir bugs imprevistos nestes certificados predefinidos, que precisam de ser corrigidos o mais cedo possível.

### <a name="if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-primary-server-or-all-the-read-replicas"></a>Se estiver a usar réplicas de leitura, preciso de realizar esta atualização apenas no servidor primário ou em todas as réplicas lidas?
Uma vez que esta atualização é uma alteração do lado do cliente, se o cliente usou para ler dados a partir do servidor de réplica, também teremos de aplicar as alterações para esses clientes. 

### <a name="do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>Temos uma consulta do lado do servidor para verificar se o SSL está a ser utilizado?
Uma vez que esta configuração é do lado do cliente, a informação não está disponível no lado do servidor.

### <a name="what-if-i-have-further-questions"></a>E se eu tiver mais perguntas?
Se tiver um plano de apoio e precisar de ajuda técnica, crie um pedido de apoio ao Azure, consulte [Como criar o pedido de suporte do Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md).