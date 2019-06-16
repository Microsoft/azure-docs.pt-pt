---
title: Gateway de dados no local
description: Um gateway no local é necessário se o seu servidor do Analysis Services no Azure que irá ligar a origens de dados no local.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 190394481f17310784f87c9e2f642eeea0b2597f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67062231"
---
# <a name="connecting-to-on-premises-data-sources-with-on-premises-data-gateway"></a>Ligar a origens de dados no local com o Gateway de dados no local
O gateway de dados no local fornece a transferência de dados segura entre origens de dados no local e os servidores do Azure Analysis Services na cloud. Além de trabalhar com vários servidores do Azure Analysis Services na mesma região, a versão mais recente do gateway também funciona com o Azure Logic Apps, o Power BI, o Power Apps e o Microsoft Flow. Pode associar vários serviços na mesma subscrição e na mesma região com um único gateway. 

Obter configuração com o gateway pela primeira vez é um processo de quatro partes:

- **Transfira e execute a configuração** -este passo instala um serviço de gateway num computador na sua organização. Também iniciar sessão no Azure através de uma conta no seu [do inquilino](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) do Azure AD. B2B do Azure (convidado) contas não são suportadas.

- **Registar o seu gateway** - neste passo, especifique um nome e recuperação de chaves para o seu gateway e selecione uma região, registar o gateway com o serviço Cloud do Gateway. O recurso de gateway pode ser registado em qualquer região, mas recomendamos que ele estar na mesma região que os servidores do Analysis Services. 

- **Criar um recurso de gateway no Azure** -neste passo, vai criar um recurso de gateway na sua subscrição do Azure.

- **Ligar os servidores para o recurso de gateway** -depois de ter um recurso de gateway na sua subscrição, pode começar a ligar os servidores ao mesmo. Pode ligar vários servidores e outros recursos, desde que estejam na mesma subscrição e na mesma região.

Para dar os primeiros passos, veja [instalar e configurar o gateway de dados no local](analysis-services-gateway-install.md).

## <a name="how-it-works"> </a>Como funciona
O gateway que instalar num computador na sua organização é executado como um serviço do Windows, **gateway de dados no local**. Este serviço local é registado no serviço de nuvem de Gateway através do Azure Service Bus. Em seguida, crie um recurso do gateway de serviço Cloud do Gateway para a sua subscrição do Azure. Os servidores do Azure Analysis Services, em seguida, são ligados ao seu recurso de gateway. Quando precisam de modelos no seu servidor ligar aos seus dados no local, origens de consultas ou processamento, um fluxo de dados e de consultas atravessa o recurso de gateway, Service bus do Azure, o serviço de gateway de dados no local e as origens de dados. 

![Como funciona](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Fluxo de dados e consultas:

1. É criada uma consulta o serviço de cloud com as credenciais encriptadas para a origem de dados no local. Em seguida, será enviado para uma fila para o gateway processar.
2. Serviço cloud do gateway analisa a consulta e envia o pedido para o [do Azure Service Bus](https://azure.microsoft.com/documentation/services/service-bus/).
3. O gateway de dados no local consulta o Azure Service Bus para pedidos pendentes.
4. O gateway obtém a consulta, desencripta as credenciais e liga-se às origens de dados com essas credenciais.
5. O gateway envia a consulta à origem de dados para execução.
6. Os resultados são enviados da origem de dados, para o gateway e, em seguida, para o serviço em nuvem e o seu servidor.

## <a name="windows-service-account"> </a>Conta de serviço do Windows
O gateway de dados no local está configurado para utilizar *NT SERVICE\PBIEgwService* para as credenciais de início de sessão de serviço do Windows. Por predefinição, possui o direito de início de sessão como um serviço; no contexto da máquina que está a instalar o gateway. Esta credencial não é a mesma conta utilizada para ligar a origens de dados no local ou a sua conta do Azure.  

Se tiver problemas com o servidor proxy devido à autenticação, convém alterar a conta de serviço do Windows para um utilizador de domínio ou conta de serviço gerida.

## <a name="ports"> </a>Portas
O gateway cria uma ligação de saída para o Azure Service Bus. Ele se comunica nas portas de saída: TCP 443 (predefinição), 5671, 5672, 9350 através da 9354.  O gateway não precisa de portas de entrada.

Recomendamos que os endereços IP para a sua região de dados na sua firewall de lista de permissões. Pode baixar o [lista de IPs do Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Esta lista é atualizada semanalmente.

> [!NOTE]
> Os endereços IP listados na lista de IPs do Azure Datacenter estão em notação CIDR. Para obter mais informações, consulte [Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).
>
>

Seguem-se os nomes de domínio completamente qualificado utilizados pelo gateway.

| Nomes de domínio | Portas de saída | Descrição |
| --- | --- | --- |
| *.powerbi.com |80 |HTTP utilizado para transferir o instalador. |
| *.powerbi.com |443 |HTTPS |
| *.analysis.windows.net |443 |HTTPS |
| *.login.windows.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Avançadas Message Queuing Protocol (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |Ouvintes no reencaminhamento do Service Bus por TCP (precisa de 443 para aquisição de token de controlo de acesso) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |Utilizado para testar a conectividade de internet se o gateway não está acessível pelo serviço do Power BI. |
| *.microsoftonline-p.com |443 |Utilizado para autenticação, dependendo da configuração. |

### <a name="force-https"></a>Forçar a comunicação HTTPS com o Azure Service Bus
Pode forçar o gateway a comunicar com o Azure Service Bus com HTTPS em vez de TCP direto; No entanto, ao fazê-lo por isso, pode reduzir significativamente o desempenho. Pode modificar os *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* ficheiro alterando o valor de `AutoDetect` para `Https`. Este ficheiro está normalmente localizado em *gateway de dados de C:\Program files\on-Premises*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```

## <a name="tenant-level-administration"></a>Administração de nível de inquilino 

Atualmente, não há um local único onde os administradores de inquilinos podem gerir todos os gateways que outros utilizadores tem instalado e configurado.  Se for um administrador inquilino, recomenda-se que peça aos utilizadores na sua organização para o adicionar como um administrador para cada gateway que instalarem. Isto permite-lhe gerir todos os gateways na sua organização através da página de definições do Gateway ou através de [comandos do PowerShell](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters#powershell-support-for-gateway-clusters). 


## <a name="faq"></a>Perguntas mais frequentes

### <a name="general"></a>Geral

**Q**: É necessário um gateway para origens de dados na cloud, como a base de dados do Azure SQL? <br/>
**A**: Não. É necessário para ligar a origens de dados no local apenas um gateway.

**Q**: O gateway tem de estar instalado no mesmo computador que a origem de dados? <br/>
**A**: Não. O gateway precisa apenas a capacidade de ligar ao servidor, normalmente na mesma rede.

<a name="why-azure-work-school-account"></a>

**Q**: Por que preciso de utilizar uma conta escolar ou profissional para iniciar sessão? <br/>
**A**: Só pode utilizar uma conta escolar ou profissional de organizacional ao instalar o gateway de dados no local. E, que conta tem de estar no mesmo inquilino que a subscrição estão a configurar o recurso de gateway no. Sua conta de início de sessão é armazenada num inquilino gerido pelo Azure Active Directory (Azure AD). Normalmente, o nome de principal de utilizador (UPN) da sua conta do Azure AD corresponde ao endereço de e-mail.

**Q**: Onde estão armazenadas as minhas credenciais? <br/>
**A**: As credenciais que introduzir para uma origem de dados são encriptadas e armazenadas no serviço Cloud do Gateway. As credenciais são desencriptadas no gateway de dados no local.

**Q**: Existem requisitos de largura de banda de rede? <br/>
**A**: Recomenda-se que a ligação de rede tenha bom débito. Cada ambiente é diferente e a quantidade de dados enviados afeta os resultados. Com o ExpressRoute pode ajudar a garantir um nível de débito entre os datacenters do Azure e no local.
Pode utilizar a aplicação Azure Speed Test de ferramenta de terceiros para ajudar a calcular o débito.

**Q**: O que é a latência de execução de consultas para uma origem de dados do gateway? O que é a melhor arquitetura? <br/>
**A**: Para reduzir a latência de rede, instale o gateway como próximo da origem de dados quanto possível. Se pode instalar o gateway na origem de dados real, este proximidade minimiza a latência introduzida. Considere também os datacenters. Por exemplo, se o serviço utiliza o datacenter E.U.A. oeste e tiver o SQL Server alojado numa VM do Azure, a VM do Azure deve estar no Oeste dos E.U.A. demasiado. Este proximidade minimiza a latência e evita custos de saída na VM do Azure.

**Q**: Como os resultados são enviados para a cloud? <br/>
**A**: Os resultados são enviados através do Azure Service Bus.

**Q**: Existem ligações de entrada para o gateway da cloud? <br/>
**A**: Não. O gateway utiliza ligações de saída para o Azure Service Bus.

**Q**: E se bloquear as ligações de saída? O que é necessário abrir? <br/>
**A**: Ver as portas e os anfitriões que o gateway utiliza.

**Q**: O que é o serviço real do Windows chamado?<br/>
**A**: Nos serviços, o gateway é chamado serviço de gateway de dados no local.

**Q**: O serviço Windows do gateway pode ser executado com uma conta do Azure Active Directory? <br/>
**A**: Não. O serviço do Windows tem de ter uma conta Windows válida. Por predefinição, o serviço é executado com o SID de serviço, o NT SERVICE\PBIEgwService.

**Q**: Como faço para obtenção de controlo um gateway? <br/>
**A**: A obtenção de controlo de um gateway (ao executar o programa de configuração/alterar no painel de controlo > programas), tem de ser um proprietário para o recurso de gateway no Azure e ter a chave de recuperação. Os proprietários de recursos de gateway podem ser configurados no controlo de acesso.

### <a name="high-availability"></a>Elevada disponibilidade e recuperação após desastre

**Q**: Como podemos ter elevada disponibilidade?  
**A**: Pode instalar um gateway noutro computador para criar um cluster. Para obter mais informações, consulte [clusters de elevada disponibilidade para o gateway de dados no local](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters) em documentos do Power BI Gateway.

**Q**: Que opções estão disponíveis para recuperação após desastre? <br/>
**A**: Pode utilizar a chave de recuperação para restaurar ou mover um gateway. Ao instalar o gateway, especifique a chave de recuperação.

**Q**: Qual é a vantagem da chave de recuperação? <br/>
**A**: A chave de recuperação fornece uma forma de migrar ou recuperar as definições do gateway após um desastre.

## <a name="troubleshooting"> </a>Resolução de problemas

**Q**: Por que motivo não vejo meu gateway na lista de instâncias de gateway ao tentar criar o recurso de gateway no Azure? <br/>
**A**: Existem duas razões possíveis. O primeiro é que um recurso já foi criado para o gateway no atual ou alguma outra subscrição. Para eliminar essa possibilidade, enumerar recursos do tipo **Gateways de dados no local** do portal. Certifique-se selecionar todas as subscrições ao enumerar todos os recursos. Assim que o recurso é criado, o gateway não aparece na lista de instâncias de gateway na experiência do portal de criar o recurso de Gateway. O segundo também é possível que a identidade do Azure AD do utilizador que instalar o gateway é diferente do utilizador iniciou sessão no portal do Azure. Para resolver, inicie sessão no portal com a mesma conta que o utilizador que instalou o gateway.

**Q**: Como posso ver que consultas estão a ser enviadas para a origem de dados no local? <br/>
**A**: Pode ativar o rastreio de consulta, que inclui as consultas que são enviadas. Não se esqueça de alterar a consulta rastreio de volta para o valor original quando terminado a resolução de problemas. Deixar o rastreio de consulta ativado cria registos maiores.

Pode também ver as ferramentas que a sua origem de dados tem para rastreio de consultas. Por exemplo, pode usar eventos expandidos ou o SQL Profiler para SQL Server e do Analysis Services.

**Q**: Onde estão os registos do gateway? <br/>
**A**: Consulte os registos mais adiante neste artigo.

### <a name="update"></a>Atualizar para a versão mais recente

Muitos problemas podem surgir quando a versão do gateway torna-se desatualizados. Como uma prática recomendada geral, certifique-se de que utiliza a versão mais recente. Se não tiver atualizado o gateway durante um mês ou mais tempo, poderá considerar instalar a versão mais recente do gateway e veja se pode reproduzir o problema.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Erro: Falha ao adicionar utilizador ao grupo. (-2147463168 PBIEgwService Performance Log Users)

Poderá receber este erro se tentar instalar o gateway num controlador de domínio, que não é suportado. Certifique-se de que implementa o gateway num computador que não seja um controlador de domínio.

## <a name="logs"></a>Registos

Ficheiros de registo são um recurso importante quando a resolução de problemas.

#### <a name="enterprise-gateway-service-logs"></a>Registos de serviço de gateway empresarial

`C:\Users\PBIEgwService\AppData\Local\Microsoft\On-premises data gateway\<yyyyymmdd>.<Number>.log`

#### <a name="configuration-logs"></a>Registos de configuração

`C:\Users\<username>\AppData\Local\Microsoft\On-premises data gateway\GatewayConfigurator.log`

#### <a name="event-logs"></a>Registos de eventos

Pode encontrar os registos do Gateway de gestão de dados e PowerBIGateway sob **registos de serviços de aplicações e**.

## <a name="next-steps"></a>Passos Seguintes
* [Instalar e configurar o gateway de dados no local](analysis-services-gateway-install.md).   
* [Gerir os serviços de análise](analysis-services-manage.md)
* [Obter dados do Azure Analysis Services](analysis-services-connect.md)
