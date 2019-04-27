---
title: Lista de verificação de segurança de recursos de infraestrutura de serviço do Azure | Documentos da Microsoft
description: Este artigo fornece um conjunto de lista de verificação de segurança de segurança de recursos de infraestrutura do Azure.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/16/2019
ms.author: tomsh
ms.openlocfilehash: 06a1903e5e27d748310c1b7846105b8069b73437
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611531"
---
# <a name="azure-service-fabric-security-checklist"></a>Lista de verificação de segurança do Azure Service Fabric
Este artigo fornece uma lista de verificação de fácil de usar que irá ajudá-lo a proteger o seu ambiente do Azure Service Fabric.

## <a name="introduction"></a>Introdução
O Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, a implementação e a gestão de microsserviços dimensionáveis e fiáveis. O Service Fabric também faz face aos desafios significativos no desenvolvimento e na gestão de aplicações na cloud. Permite, assim, que os programadores e administradores evitem problemas complexos de infraestrutura e se concentrem na implementação de cargas de trabalho exigentes e fundamentais que sejam dimensionáveis, fiáveis e geríveis.

## <a name="checklist"></a>Lista de verificação
Utilize a lista de verificação seguinte para ajudar a tornar-se de que ainda não passa despercebido problemas importantes no gerenciamento e configuração de uma solução de Azure Service Fabric segura.


|Categoria da lista de verificação| Descrição |
| ------------ | -------- |
|[Controlo de acesso baseado em funções (RBAC)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles) | <ul><li>Controlo de acesso permite ao administrador de cluster limitar o acesso a determinadas operações de cluster para diferentes grupos de usuários, fazendo com que o cluster mais seguro.</li><li>Os administradores têm acesso total às capacidades de gestão (incluindo recursos de leitura/gravação). </li><li> Por predefinição, os utilizadores, tem apenas acesso de leitura às capacidades de gestão (por exemplo, capacidades de consulta) e a capacidade de resolver a aplicações e serviços.</li></ul>|
|[Certificados X.509 e o Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) | <ul><li>[Certificados](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates) utilizado em clusters de cargas de trabalho de produção em execução devem ser criadas com um serviço de certificado de servidor Windows corretamente configurado ou obtidas a partir de um aprovados [autoridade de certificado (AC)](https://en.wikipedia.org/wiki/Certificate_authority).</li><li>Nunca usar qualquer [temporário ou certificados de teste](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-create-temporary-certificates-for-use-during-development) em produção, que são criados com ferramentas como [MakeCert.exe](https://msdn.microsoft.com/library/windows/desktop/aa386968.aspx). </li><li>Pode utilizar um [certificado autoassinado](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) , mas, deve apenas fazer isso para clusters de teste e não na produção.</li></ul>|
|[Segurança do cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) | <ul><li>Os cenários de segurança de cluster incluem a segurança de nó para nó, segurança de cliente para nó, [controlo de acesso baseado em funções (RBAC)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).</li></ul>|
|[Autenticação do cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) | <ul><li>Autentica [comunicação nó a nó](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-security.md) para Federação de cluster. </li></ul>|
|[Autenticação de servidor](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) | <ul><li>Autentica o [pontos finais de gestão do cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal) para um cliente de gestão.</li></ul>|
|[Segurança de aplicações](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)| <ul><li>Encriptação e desencriptação de valores de configuração de aplicações.</li><li>   Encriptação de dados entre os nós durante a replicação.</li></ul>|
|[Certificado de cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) | <ul><li>Este certificado é necessário para proteger a comunicação entre os nós num cluster.</li><li>    Defina o thumbprint do certificado principal na secção de Thumbprint e que o elemento secundário nas variáveis de ThumbprintSecondary.</li></ul>|
|[ServerCertificate](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security)| <ul><li>Este certificado é apresentado para o cliente quando tentar ligar a este cluster. Pode usar dois certificados de servidor diferente, um site primário e um secundário para atualização.</li></ul>|
|ClientCertificateThumbprints| <ul><li>Este é um conjunto de certificados que pretende instalar nos clientes autenticados. </li></ul>|
|ClientCertificateCommonNames| <ul><li>Defina o nome comum do certificado de cliente primeiro para o CertificateCommonName. O CertificateIssuerThumbprint é o thumbprint para o emissor deste certificado. </li></ul>|
|ReverseProxyCertificate| <ul><li>Este é um certificado opcional que pode ser especificado se pretender proteger o seu [Proxy inverso do](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy). </li></ul>|
|Cofre de Chaves| <ul><li>Utilizado para gerir certificados para clusters do Service Fabric no Azure.  </li></ul>|


## <a name="next-steps"></a>Passos Seguintes

- [Práticas recomendadas de segurança de Service Fabric](azure-service-fabric-security-best-practices.md)
- [Processo de atualização do Cluster do Service Fabric e as expectativas da sua](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade)
- [Gestão das suas aplicações do Service Fabric no Visual Studio](https://docs.microsoft.com/azure/service-fabric/service-fabric-manage-application-in-visual-studio).
- [Introdução do modelo de estado de funcionamento do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction).
