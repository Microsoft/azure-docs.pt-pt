---
title: 'Azure Active Directory Domain Services: Cenários de implementação | Documentos da Microsoft'
description: Cenários de implementação para o Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/21/2017
ms.author: ergreenl
ms.openlocfilehash: 0659586512b36c51c5058271fa5e1bdb46efbc3b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60416898"
---
# <a name="deployment-scenarios-and-use-cases"></a>Cenários de implementação e casos de utilização
Nesta secção, vamos ver alguns cenários e casos de utilização que tiram partido dos serviços de domínio do Azure Active Directory (AD).

## <a name="secure-easy-administration-of-azure-virtual-machines"></a>Administração segura e fácil de máquinas virtuais do Azure
Pode utilizar o Azure Active Directory Domain Services para gerir máquinas virtuais do Azure de forma otimizada. Máquinas virtuais do Azure pode ser associadas ao domínio gerido, permitindo-lhe utilizar as credenciais empresariais do AD para iniciar sessão. Esta abordagem ajuda a evitar as preocupações de gestão de credenciais, tais como a manutenção de contas de administrador local em cada uma das máquinas virtuais do Azure.

Máquinas de virtuais de servidor que estão associadas ao domínio gerido também podem ser geridas e protegidas através de diretiva de grupo. Pode aplicar linhas de base de segurança necessárias para máquinas virtuais do Azure e bloqueá-los em conformidade com diretrizes de segurança corporativa. Por exemplo, pode utilizar as capacidades de gestão de política de grupo para restringir os tipos de aplicativos que podem ser iniciados nestas máquinas virtuais.

![Administração simplificada de máquinas virtuais do Azure](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Quando servidores e outras infraestruturas atinge o final da vida, a Contoso está migrando muitos aplicativos atualmente alojados no local para a cloud. O padrão IT atual estipula que servidores que alojam aplicações da empresa tem de ser associado a um domínio e gerenciado usando a diretiva de grupo. Do contoso administrador de TI prefere máquinas de virtuais de associação de domínio implementadas no Azure, para facilitar a administração. Como resultado, os administradores e utilizadores podem iniciar sessão utilizando as credenciais da empresa. Ao mesmo tempo, as máquinas podem ser configuradas para estar em conformidade com as linhas de base de segurança necessárias usando a diretiva de grupo. Contoso prefere não precisa de implementar, monitorizar e gerir controladores de domínio no Azure para proteger máquinas virtuais do Azure. Portanto, o Azure AD Domain Services é uma excelente escolha para este caso de utilização.

**Notas de implementação**

Considere os seguintes pontos importantes para este cenário de implementação:

* Domínios geridos fornecidos pelo Azure AD Domain Services fornecem uma única estrutura de UO (unidade organizacional) simples por predefinição. Todos os computadores associados a um domínio de residir numa única UO simples. No entanto pode optar por criar UOs personalizadas.
* Serviços de domínio do Azure AD suporta a política de grupo simples na forma de um GPO incorporado cada para os utilizadores e computadores contentores. Pode criar GPOs personalizados e direcioná-las para UOs personalizadas.
* O Azure AD Domain Services suporta o esquema de objeto de computador base do AD. Não é possível expandir o esquema do objeto de computador.

## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-bind-authentication-to-azure-infrastructure-services"></a>Migração lift-and-shift uma aplicação no local que utiliza a autenticação do enlace de LDAP para serviços de infraestrutura do Azure
![Enlace de LDAP](./media/active-directory-domain-services-scenarios/ldap-bind.png)

A Contoso tiver uma aplicação no local que tenha sido comprada de um ISV há muitos anos. A aplicação está atualmente no modo de manutenção por de ISV e pedir alterações à aplicação é proibitivo para Contoso. Esta aplicação tem um baseada na web front-end que recolhe as credenciais de utilizador com um formulário da web e, em seguida, autentica os utilizadores através de um enlace LDAP para a empresa do Active Directory. Contoso pretende migrar esta aplicação nos serviços de infraestrutura do Azure. É desejável que o aplicativo funciona tal como está, sem exigir alterações. Além disso, os utilizadores devem conseguir autenticar com as respetivas credenciais empresariais atuais e sem ter de voltar a preparar os usuários façam coisas de forma diferente. Em outras palavras, os utilizadores finais devem ser óbvia de onde a aplicação está em execução e a migração deve ser transparente aos mesmos.

**Notas de implementação**

Considere os seguintes pontos importantes para este cenário de implementação:

* Certifique-se de que o aplicativo não precisa de modificar/escrita para o diretório. Acesso de escrita LDAP para domínios geridos fornecida pelo Azure AD Domain Services não é suportado.
* Não é possível alterar as palavras-passe diretamente contra o domínio gerido. Os utilizadores finais podem alterar a palavra-passe ou usando o mecanismo de alteração de palavra-passe self-service do Azure AD ou no diretório no local. Estas alterações são automaticamente sincronizados e fiquem disponíveis no domínio gerido.

## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-read-to-access-the-directory-to-azure-infrastructure-services"></a>Migração lift-and-shift de uma aplicação no local que utiliza o LDAP leitura para aceder ao diretório para serviços de infraestrutura do Azure
A Contoso tiver uma aplicação de linha de negócio (LOB) no local que foi desenvolvida quase uma década atrás. Esta aplicação está ciente de diretório e foi concebida para trabalhar com o Windows Server AD. O aplicativo usa LDAP (Lightweight Directory Access Protocol) para ler informações/atributos sobre os utilizadores do Active Directory. A aplicação não modificar atributos ou caso contrário escrevam no diretório. Contoso quer migrar esta aplicação nos serviços de infraestrutura do Azure e extinguir o hardware de locais de classificação por vencimento aloja atualmente esta aplicação. A aplicação não pode ser reescrita para usar o diretório moderno a APIs, como o Azure AD Graph API baseada em REST. Por conseguinte, uma opção de migração lift-and-shift for o pretendido por meio das quais a aplicação pode ser migrada para executar na cloud, sem modificar o código ou reescrever o aplicativo.

**Notas de implementação**

Considere os seguintes pontos importantes para este cenário de implementação:

* Certifique-se de que o aplicativo não precisa de modificar/escrita para o diretório. Acesso de escrita LDAP para domínios geridos fornecida pelo Azure AD Domain Services não é suportado.
* Certifique-se de que a aplicação não necessita de um esquema do Active Directory expandido personalizado. Extensões de esquema não são suportadas no Azure AD Domain Services.

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure-infrastructure-services"></a>Migrar uma aplicação de serviço ou daemon no local para serviços de infraestrutura do Azure
Alguns aplicativos consistem em várias camadas, onde um dos escalões precisa de realizar chamadas autenticadas para uma camada de back-end, como uma camada de base de dados. Contas de serviço do Active Directory são frequentemente utilizadas para estes casos de utilização. Pode migração lift-and-shift tais aplicativos em serviços de infraestrutura do Azure e utilizar Azure AD Domain Services para as necessidades de identidade desses aplicativos. Pode optar por utilizar a mesma conta de serviço que é sincronizada a partir do seu diretório no local para o Azure AD. Em alternativa, pode criar primeiro uma UO personalizada e, em seguida, criar uma conta de serviço separado nessa UO, implantar esses aplicativos.

![Conta de serviço a utilizar WIA](./media/active-directory-domain-services-scenarios/wia-service-account.png)

A Contoso tiver um aplicativo de cofre personalizados internos que inclui um front-end da web, um SQL server e um servidor FTP de back-end. Autenticação integrada do Windows de contas de serviço é utilizada para autenticar web front-end para o servidor FTP. O front-end da web está configurado para ser executado como uma conta de serviço. O servidor de back-end está configurado para autorizar o acesso da conta de serviço para o front-end da web. Contoso prefere não tem de implementar uma máquina virtual do controlador de domínio na cloud para mover esta aplicação nos serviços de infraestrutura do Azure. Do contoso administrador de TI pode implementar os servidores que alojam o front-end da web, o SQL server e o servidor de FTP para máquinas virtuais do Azure. Estas máquinas, em seguida, estão associadas a um domínio gerido do Azure AD Domain Services. Em seguida, eles podem utilizar a mesma conta de serviço em seu diretório no local para fins de autenticação da aplicação. Esta conta de serviço está sincronizada com o domínio gerido do Azure AD Domain Services e está disponível para utilização.

**Notas de implementação**

Considere os seguintes pontos importantes para este cenário de implementação:

* Certifique-se de que a aplicação utiliza o nome de utilizador/palavra-passe para autenticação. Autenticação de certificado/smart card com base não é suportada pelo Azure AD Domain Services.
* Não é possível alterar as palavras-passe diretamente contra o domínio gerido. Os utilizadores finais podem alterar a palavra-passe ou usando o mecanismo de alteração de palavra-passe self-service do Azure AD ou no diretório no local. Estas alterações são automaticamente sincronizados e fiquem disponíveis no domínio gerido.

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Implementações no Azure dos serviços de ambiente de trabalho remoto do Windows Server
Pode utilizar o Azure AD Domain Services para fornecer os serviços de domínio do AD geridos para os seus servidores de ambiente de trabalho remotos, implementados no Azure.

Para obter mais informações sobre este cenário de implementação, consulte como [integrar os serviços de domínio do Azure AD com a implementação de RDS](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-azure-adds).


## <a name="domain-joined-hdinsight-clusters-preview"></a>Clusters do HDInsight associados a um domínio (pré-visualização)
Pode configurar um cluster do HDInsight do Azure que está associado a um domínio gerido do Azure AD Domain Services com o Apache Ranger ativada. Criar e aplicar políticas do Hive através do Apache Ranger para permitir que os utilizadores (por exemplo, os cientistas de dados) ligar ao Hive usando ferramentas baseadas em ODBC, por exemplo, Excel, Tableau etc. A Microsoft está trabalhando sobre como adicionar outras cargas de trabalho, como o HBase e Storm, Spark para HDInsight associado a um domínio em breve.

Para obter mais informações sobre este cenário de implementação, consulte como [configurar clusters do HDInsight associados a um domínio](../hdinsight/domain-joined/apache-domain-joined-configure.md)
