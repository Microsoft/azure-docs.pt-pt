---
title: Resolução de problemas diagnostica conectividade no tempo de funcionação da integração SSIS
description: Este artigo fornece orientações de resolução de problemas para diagnosticar conectividade no tempo de funcionamento da integração SSIS
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: meiyl
author: meiyl
ms.reviewer: sawinark
manager: yidetu
ms.date: 06/07/2020
ms.openlocfilehash: 8e520048a6067f134e847953f4f4aa0598d9926e
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86172715"
---
# <a name="troubleshoot-diagnose-connectivity-in-the-ssis-integration-runtime"></a>Resolução de problemas diagnostica conectividade no tempo de funcionação da integração SSIS

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Se encontrar problemas de conectividade durante a execução de pacotes SSIS no tempo de funcionamento da integração SSIS, especialmente se o seu tempo de integração SSIS se juntar à rede virtual Azure. Pode tentar diagnosticar problemas de auto-diagnóstico utilizando esta funcionalidade de conectividade de diagnóstico no âmbito da página de execução de integração SSIS de monitorização do portal Azure Data Factory. 

 ![Página monitor - diagnosticar página monitor de conectividade ](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-diagnose-connectivity.png) ![ - conexão de teste](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-test-connection.png)
 
Este artigo inclui os erros mais comuns que poderá encontrar quando estiver a testar a ligação no tempo de funcionação da integração SSIS. Descreve as potenciais causas e ações para resolver os erros. 

## <a name="common-errors-potential-causes-and-recommendation-solutions"></a>Erros comuns, causas potenciais e soluções de recomendação

### <a name="error-code-invalidinput"></a>Código de erro: InvalidInput.
* **Error message**: Verifique se a sua entrada está correta.
* **Causas potenciais:** A sua entrada está errada.
* **Recomendação:** Por favor, verifique a sua entrada.

### <a name="error-code-firewallornetworkissue"></a>Código de erro: FirewallOrNetworkIssue.
* **Mensagem de erro**: Verifique se esta porta está aberta na sua firewall/servidor/NSG e a rede está estável.
* **Causas potenciais:** 
  * O seu servidor não abre esta porta.
  * O seu grupo de segurança de rede é negado tráfego de saída neste porto
  * A sua firewall NVA/Azure/On-prem não abre esta porta.
* **Recomendação:** 
  * Abra esta porta no servidor.
  * Atualize o grupo de segurança da rede para permitir o tráfego de saída neste porto.
  * Abra esta porta na firewall NVA/Azure/Firewall On-prem.

### <a name="error-code-misconfigureddnssettings"></a>Código de erro: Configurações deDnssettings mal configuradas.
* **Mensagem de erro**: Se estiver a utilizar o seu próprio servidor DNS no VNet, acompanhado pelo seu Azure-SSIS IR, verifique se pode resolver o nome de anfitrião.
* **Causas potenciais:** 
  *  O problema do DNS personalizado
  *  Não está a usar um nome de domínio totalmente qualificado (FQDN) para o seu nome de anfitrião privado
* **Recomendação:** 
  *  Corrija o seu problema de DNS personalizado para se certificar de que pode resolver o nome do anfitrião.
  *  Utilize o nome de domínio totalmente qualificado (FQDN) , por exemplo, utilize <your_private_server>.contoso.com em vez de <your_private_server>, uma vez que o Azure-SSIS IR não anexa automaticamente o seu próprio sufixo DNS.

### <a name="error-code-servernotallowremoteconenction"></a>Código de erro: ServerNotAllowRemoteConenction.
* **Error message**: Verifique se o seu servidor permite ligações remotas de TCP através desta porta.
* **Causas potenciais:** 
  *  A firewall do servidor não permite ligações remotas de TCP.
  *  O seu servidor não está online.
* **Recomendação:** 
  *  Permitir ligações tele remotas de TCP na firewall do servidor.
  *  Inicie o servidor.
   
### <a name="error-code-misconfigurednsgsettings"></a>Código de erro: Configurações Mal configuradasNsgSettings.
* **Error message**: Verifique se o NSG do seu VNet permite o tráfego de saída através desta porta. Se estiver a utilizar o Azure ExpressRoute e um UDR, verifique se esta porta está aberta na sua firewall/servidor.
* **Causas potenciais:** 
  *  O seu grupo de segurança de rede é negado tráfego de saída neste porto.
  *  A sua firewall NVA/Azure/On-prem não abre esta porta.
* **Recomendação:** 
  *  Atualize o grupo de segurança da rede para permitir o tráfego de saída neste porto.
  *  Abra esta porta na firewall NVA/Azure/Firewall On-prem.

### <a name="error-code-genericissues"></a>Código de erro: Genéricos.
* **Mensagem de erro**: A ligação de teste falhou devido a problemas genéricos.
* **Causas potenciais:** A ligação de teste encontrou um problema temporário geral.
* **Recomendação:** Por favor, re-teste a ligação de teste mais tarde. Se a reagem não ajudar, contacte a equipa de suporte da Azure Data Factory.


### <a name="error-code-pspingexecutiontimeout"></a>Código de erro: PSPingExecutionTimeout.
* **Error message**: Test connection timeout, try again later.
* **Causas potenciais:** A conectividade do teste está fora de questão.
* **Recomendação:** Por favor, re-teste a ligação de teste mais tarde. Se a reagem não ajudar, contacte a equipa de suporte da Azure Data Factory.

### <a name="error-code-networkinstable"></a>Código de erro: NetworkInstable.
* **Mensagem de erro**: A ligação de teste foi sucedida de forma irregular devido à instabilidade da rede.
* **Causas potenciais:** Problema de rede transitório.
* **Recomendação:** Verifique se o servidor ou a rede de firewall estão estáveis.

## <a name="next-steps"></a>Passos seguintes

- Desloquem os vossos pacotes. Para mais informações, consulte [implementar um projeto SSIS para Azure com SSMS.](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-deploy-ssms)
- Coloquem os seus pacotes. Para mais informações, consulte [os pacotes Run SSIS em Azure com SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-run-ssms).
- Agende os seus pacotes. Para mais informações, consulte [os pacotes Schedule SSIS em Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15).

