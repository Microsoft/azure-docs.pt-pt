---
title: Utilize a função de conectividade de diagnóstico no tempo de funcionação da integração SSIS
description: Problemas de conexão de resolução de problemas no tempo de funcionação da integração SSIS utilizando a função de conectividade do diagnóstico.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: meiyl
author: meiyl
ms.reviewer: sawinark
manager: yidetu
ms.date: 06/07/2020
ms.openlocfilehash: ede36b20353f00ed9a4f80bec2d7bc5a3512a9ea
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637960"
---
# <a name="use-the-diagnose-connectivity-feature-in-the-ssis-integration-runtime"></a>Utilize a função de conectividade de diagnóstico no tempo de funcionação da integração SSIS

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Pode encontrar problemas de conectividade ao executar pacotes SQL Server Integration Services (SSIS) no tempo de funcionamento da integração SSIS. Estes problemas ocorrem especialmente se o seu tempo de integração SSIS se juntar à rede virtual Azure.

Resolver problemas de conectividade através da utilização da função *de conectividade* do diagnóstico para testar ligações. A funcionalidade encontra-se na página de execução de integração SSIS de monitorização do portal Azure Data Factory.

 ![Página monitor - diagnosticar conectividade](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-diagnose-connectivity.png)

 ![Página monitor - ligação de teste](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-test-connection.png)

Utilize as seguintes secções para saber mais sobre os erros mais comuns que ocorrem quando está a testar ligações. Cada secção descreve:

- Código de erro
- Mensagem de erro
- Causas potenciais do erro
- Solução recomendada

## <a name="error-code-invalidinput"></a>Código de erro: InvalidInput

- **Error message** : "Please check your input is correct."
- **Causa potencial:** A sua entrada está incorreta.
- **Recomendação** : Verifique a sua entrada.

## <a name="error-code-firewallornetworkissue"></a>Código de erro: FirewallOrNetworkIssue

- **Error message** : "Please check that this porta is open on your firewall/server/NSG and the network is estável."
- **Causas potenciais:**
  - O seu servidor não abre a porta.
  - O seu grupo de segurança de rede é negado tráfego de saída no porto.
  - A sua firewall NVA/Azure Firewall/no local não abre a porta.
- **Recomendações:**
  - Abra a porta no servidor.
  - Atualize o grupo de segurança da rede para permitir o tráfego de saída no porto.
  - Abra a porta na firewall NVA/Azure Firewall/no local.

## <a name="error-code-misconfigureddnssettings"></a>Código de erro: Configurações deDnssettings mal configuradas

- **Error message** : "If you're using your own DNS server in the VNet joined by your Azure-SSIS IR, check that it can resolve your host name."
- **Causas potenciais:**
  -  Há um problema com o seu DNS personalizado.
  -  Não está a usar um nome de domínio totalmente qualificado (FQDN) para o seu nome de anfitrião privado.
- **Recomendações:**
  -  Corrija o seu problema de DNS personalizado para se certificar de que pode resolver o nome do anfitrião.
  -  Utilize o FQDN. O Azure-SSIS IR não anexará automaticamente o seu próprio sufixo DNS. Por exemplo, utilize **<your_private_server>.contoso.com** em vez de **<your_private_server>** .

## <a name="error-code-servernotallowremoteconnection"></a>Código de erro: ServerNotAllowRemoteConnection

- **Error message** : "Please check that your server allows remote TCP connections through this port."
- **Causas potenciais:**
  -  A firewall do servidor não permite ligações remotas de TCP.
  -  O seu servidor não está online.
- **Recomendações:**
  -  Permitir ligações tele remotas de TCP na firewall do servidor.
  -  Inicie o servidor.
   
## <a name="error-code-misconfigurednsgsettings"></a>Código de erro: MisconfiguredNsgSettings

- **Error message** : "Please verifi that the NSG of your VNet allow outbound traffic through this port. Se estiver a utilizar o Azure ExpressRoute e um UDR, verifique se esta porta está aberta na sua firewall/servidor."
- **Causas potenciais:**
  -  O seu grupo de segurança de rede é negado tráfego de saída no porto.
  -  A sua firewall NVA/Azure Firewall/no local não abre a porta.
- **Recomendação:**
  -  Atualize o grupo de segurança da rede para permitir o tráfego de saída no porto.
  -  Abra a porta na firewall NVA/Azure Firewall/no local.

## <a name="error-code-genericissues"></a>Código de erro: GenéricoSs

- **Error message** : "A ligação de teste falhou devido a problemas genéricos."
- **Causa potencial** : A ligação de teste encontrou um problema temporário geral.
- **Recomendação** : Recandidutar a ligação de teste mais tarde. Se voltar a tentar não ajudar, contacte a equipa de suporte da Azure Data Factory.

## <a name="error-code-pspingexecutiontimeout"></a>Código de erro: PSPingExecutionTimeout

- **Error message** : "Test connection timeout, try again later."
- **Causa potencial** : Testar a conectividade cronometrada.
- **Recomendação** : Recandidutar a ligação de teste mais tarde. Se voltar a tentar não ajudar, contacte a equipa de suporte da Azure Data Factory.

## <a name="error-code-networkinstable"></a>Código de erro: NetworkInstable

- **Error message** : "A ligação de teste foi sucedida de forma irregular devido à instabilidade da rede."
- **Causa potencial** : Problema de rede transitório.
- **Recomendação** : Verifique se o servidor ou a rede de firewall estão estáveis.

## <a name="next-steps"></a>Passos seguintes

- [Implementar um projeto SSIS para a Azure com sSMS](/sql/integration-services/ssis-quickstart-deploy-ssms)
- [Executar pacotes SSIS em Azure com SSMS](/sql/integration-services/ssis-quickstart-run-ssms)
- [Agendar pacotes SSIS em Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15)