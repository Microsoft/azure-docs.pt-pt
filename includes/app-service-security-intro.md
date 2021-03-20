---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 04/15/2020
ms.author: cephalin
ms.openlocfilehash: 0fccb1ed092190f88d197c9bf2cd17678a4d1731
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "83649045"
---
Os componentes da plataforma do Serviço de Aplicações, incluindo VMs Azure, armazenamento, conexões de rede, quadros web, funcionalidades de gestão e integração, estão ativamente protegidos e endurecidos. O Serviço de Aplicações passa por verificações vigorosas de conformidade numa base contínua para garantir que:

- Os recursos da sua aplicação estão [protegidos](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) a partir dos recursos Azure dos outros clientes.
- [As instâncias VM e o software de tempo de execução são regularmente atualizados](../articles/app-service/overview-patch-os-runtime.md) para resolver vulnerabilidades recém-descobertas. 
- A comunicação de segredos (como cordas de ligação) entre a sua app e outros recursos Azure (como [a SQL Database)](https://azure.microsoft.com/services/sql-database/)permanece dentro do Azure e não ultrapassa quaisquer limites de rede. Os segredos são sempre encriptados quando armazenados.
- Toda a comunicação sobre as funcionalidades de conectividade do Serviço de Aplicações, como [a ligação híbrida,](../articles/app-service/app-service-hybrid-connections.md)é encriptada. 
- As ligações com ferramentas de gestão remota como Azure PowerShell, Azure CLI, Azure SDKs, REST APIs, estão todas encriptadas.
- A gestão de ameaças 24 horas protege a infraestrutura e a plataforma contra malware, negação de serviço distribuída (DDoS), man-in-the-middle (MITM) e outras ameaças.

Para obter mais informações sobre infraestruturas e segurança da plataforma em Azure, consulte [o Azure Trust Center](https://azure.microsoft.com/overview/trusted-cloud/).