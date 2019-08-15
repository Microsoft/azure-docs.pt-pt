---
title: Lista de verificação de segurança do Azure Service Fabric | Microsoft Docs
description: Este artigo fornece um conjunto de lista de verificação para segurança de segurança da malha do Azure.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/16/2019
ms.author: tomsh
ms.openlocfilehash: c30b70d2fccb7580dcb94c2322c0ad3a52461f34
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68927732"
---
# <a name="azure-service-fabric-security-checklist"></a>Lista de verificação de segurança do Azure Service Fabric
Este artigo fornece uma lista de verificação fácil de usar que ajudará você a proteger seu ambiente de Service Fabric do Azure.

## <a name="introduction"></a>Introdução
O Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, a implementação e a gestão de microsserviços dimensionáveis e fiáveis. O Service Fabric também faz face aos desafios significativos no desenvolvimento e na gestão de aplicações na cloud. Permite, assim, que os programadores e administradores evitem problemas complexos de infraestrutura e se concentrem na implementação de cargas de trabalho exigentes e fundamentais que sejam dimensionáveis, fiáveis e geríveis.

## <a name="checklist"></a>Lista de verificação
Use a lista de verificação a seguir para ajudá-lo a garantir que você não tenha ignorado nenhum problema importante no gerenciamento e na configuração de uma solução de Service Fabric segura do Azure.


|Categoria da lista de verificação| Descrição |
| ------------ | -------- |
|[RBAC (controle de acesso baseado em função)](../../service-fabric/service-fabric-cluster-security-roles.md) | <ul><li>O controle de acesso permite que o administrador de cluster limite o acesso a determinadas operações de cluster para diferentes grupos de usuários, tornando o cluster mais seguro.</li><li>Os administradores têm acesso completo aos recursos de gerenciamento (incluindo os recursos de leitura/gravação). </li><li> Os usuários, por padrão, têm apenas acesso de leitura aos recursos de gerenciamento (por exemplo, recursos de consulta) e a capacidade de resolver aplicativos e serviços.</li></ul>|
|[Service Fabric e certificados X. 509](../../service-fabric/service-fabric-cluster-security.md) | <ul><li>Os [certificados](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates) usados em clusters que executam cargas de trabalho de produção devem ser criados usando um serviço de certificado do Windows Server configurado corretamente ou obtidos de uma [autoridade de certificação (CA)](https://en.wikipedia.org/wiki/Certificate_authority)aprovada.</li><li>Nunca use nenhum [certificado temporário ou de teste](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-create-temporary-certificates-for-use-during-development) em produção criado com ferramentas como [MakeCert. exe](https://msdn.microsoft.com/library/windows/desktop/aa386968.aspx). </li><li>Você pode usar um [certificado autoassinado](../../service-fabric/service-fabric-windows-cluster-x509-security.md) , mas só deve fazer isso para clusters de teste e não para produção.</li></ul>|
|[Segurança do cluster](../../service-fabric/service-fabric-cluster-security.md) | <ul><li>Os cenários de segurança de cluster incluem segurança de nó para nó, segurança de cliente para nó, [controle de acesso baseado em função (RBAC)](../../service-fabric/service-fabric-cluster-security-roles.md).</li></ul>|
|[Autenticação de cluster](../../service-fabric/service-fabric-cluster-creation-via-arm.md) | <ul><li>Autentica [a comunicação de nó para nó](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-security.md) para a Federação de cluster. </li></ul>|
|[Autenticação do servidor](../../service-fabric/service-fabric-cluster-creation-via-arm.md) | <ul><li>Autentica os [pontos de extremidade de gerenciamento de cluster](../../service-fabric/service-fabric-cluster-creation-via-portal.md) para um cliente de gerenciamento.</li></ul>|
|[Segurança de aplicações](../../service-fabric/service-fabric-cluster-creation-via-arm.md)| <ul><li>Criptografia e descriptografia de valores de configuração de aplicativo.</li><li>   Criptografia de dados entre nós durante a replicação.</li></ul>|
|[Certificado do cluster](../../service-fabric/service-fabric-windows-cluster-x509-security.md) | <ul><li>Esse certificado é necessário para proteger a comunicação entre os nós em um cluster.</li><li>    Defina a impressão digital do certificado primário na seção impressão digital e a do secundário nas variáveis ThumbprintSecondary.</li></ul>|
|[ServerCertificate](../../service-fabric/service-fabric-windows-cluster-x509-security.md)| <ul><li>Esse certificado é apresentado ao cliente quando ele tenta se conectar a esse cluster. Você pode usar dois certificados de servidor diferentes, um primário e um secundário para atualização.</li></ul>|
|ClientCertificateThumbprints| <ul><li>Esse é um conjunto de certificados que você deseja instalar nos clientes autenticados. </li></ul>|
|ClientCertificateCommonNames| <ul><li>Defina o nome comum do primeiro certificado de cliente para o CertificateCommonName. O CertificateIssuerThumbprint é a impressão digital para o emissor deste certificado. </li></ul>|
|ReverseProxyCertificate| <ul><li>Esse é um certificado opcional que pode ser especificado se você quiser proteger seu [proxy reverso](../../service-fabric/service-fabric-reverseproxy.md). </li></ul>|
|Cofre de Chaves| <ul><li>Usado para gerenciar certificados para Service Fabric clusters no Azure.  </li></ul>|


## <a name="next-steps"></a>Passos Seguintes

- [Práticas recomendadas de segurança Service Fabric](service-fabric-best-practices.md)
- [Service Fabric processo de atualização de cluster e as expectativas de você](../../service-fabric/service-fabric-cluster-upgrade.md)
- [Gerenciando seus aplicativos Service Fabric no Visual Studio](../../service-fabric/service-fabric-manage-application-in-visual-studio.md).
- [Introdução ao modelo de integridade Service Fabric](../../service-fabric/service-fabric-health-introduction.md).
