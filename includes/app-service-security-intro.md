---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 04/15/2020
ms.author: cephalin
ms.openlocfilehash: 0fccb1ed092190f88d197c9bf2cd17678a4d1731
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649045"
---
Os componentes da plataforma do App Service, incluindo VMs Azure, armazenamento, conexões de rede, quadros web, funcionalidades de gestão e integração, são ativamente protegidos e endurecidos. O Serviço de Aplicações passa por controlos de conformidade vigorosos numa base contínua para garantir que:

- Os recursos da sua aplicação estão [protegidos](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) dos recursos Azure dos outros clientes.
- [As instâncias vM e o software de tempo de execução são regularmente atualizados](../articles/app-service/overview-patch-os-runtime.md) para resolver vulnerabilidades recentemente descobertas. 
- A comunicação de segredos (como cordas de ligação) entre a sua app e outros recursos Azure (como a Base de [Dados SQL)](https://azure.microsoft.com/services/sql-database/)permanece dentro do Azure e não ultrapassa quaisquer limites de rede. Os segredos são sempre encriptados quando armazenados.
- Toda a comunicação sobre as funcionalidades de conectividade do Serviço app, como [a ligação híbrida,](../articles/app-service/app-service-hybrid-connections.md)está encriptada. 
- As ligações com ferramentas de gestão remota como Azure PowerShell, Azure CLI, Azure SDKs, REST APIs, estão todas encriptadas.
- A gestão de ameaças 24 horas protege a infraestrutura e plataforma contra malware, distribuição de negação de serviço (DDoS), man-in-the-middle (MITM) e outras ameaças.

Para obter mais informações sobre infraestruturas e segurança na plataforma em Azure, consulte [o Azure Trust Center.](https://azure.microsoft.com/overview/trusted-cloud/)