---
title: O que é o Azure Firewall?
description: O Azure Firewall é um serviço de segurança de rede gerido e com base na cloud que protege os recursos da Rede Virtual do Azure.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc, contperfq1
ms.date: 11/09/2020
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: ab5a4717f32269f34a9351cb0ffa3796b89ccd6e
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94412753"
---
# <a name="what-is-azure-firewall"></a>O que é o Azure Firewall?

![Certificação ICSA](media/overview/icsa-cert-firewall-small.png)

O Azure Firewall é um serviço de segurança de rede gerido e com base na cloud que protege os recursos da Rede Virtual do Azure. É uma firewall como um serviço com monitorização de estado com alta disponibilidade integrada e escalabilidade da cloud irrestrita.

![Descrição geral das firewalls](media/overview/firewall-threat.png)

Pode criar, impor e registar centralmente políticas de conectividade de rede e aplicações entre subscrições e redes virtuais. O Azure Firewall utiliza um endereço IP público estático para os recursos de rede virtual que permite às firewalls externas identificar o tráfego com origem na sua rede virtual.  O serviço está totalmente integrado no Azure Monitor para fins de registo e análise.

## <a name="features"></a>Funcionalidades

Para saber mais sobre as funcionalidades do Azure Firewall, consulte [as funcionalidades do Azure Firewall](features.md).

## <a name="known-issues"></a>Problemas conhecidos

O Azure Firewall tem os seguintes problemas conhecidos:

|Problema  |Description  |Mitigação  |
|---------|---------|---------|
As regras de filtragem de rede para protocolos não TCP/UDP (por exemplo, ICMP) não funcionam para o tráfego vinculado à Internet|As regras de filtragem da rede para protocolos não-TCP/UDP não funcionam com o SNAT no seu endereço IP público. Os protocolos não TCP/UDP são suportados entre VNets e sub-redes spoke.|O Azure Firewall utiliza o Balanceador de Carga Standard [que não suporta atualmente SNAT para protocolos IP](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview). Estamos a explorar opções para apoiar este cenário num futuro lançamento.|
|Suporte do PowerShell e CLI em falta para ICMP|A Azure PowerShell e CLI não suportam o ICMP como um protocolo válido nas regras de rede.|Ainda é possível utilizar o ICMP como protocolo através do portal e da API REST. Estamos a trabalhar para adicionar ICMP na PowerShell e na CLI em breve.|
|As etiquetas FQDN requerem um protocolo: porta a definir|As regras de aplicação com tags FQDN requerem porta: definição de protocolo.|Pode utilizar **https** como a porta: valor de protocolo. Estamos a trabalhar para tornar este campo opcional quando as etiquetas FQDN são usadas.|
|Mover uma firewall para um grupo de recursos diferente ou subscrição não é suportado|Mover uma firewall para um grupo de recursos diferente ou subscrição não é suportado.|O suporte desta funcionalidade está no nosso roteiro. Para mover uma firewall para um grupo de recursos ou uma subscrição diferente, tem de eliminar a instância atual e recriá-la no novo grupo de recursos ou subscrição.|
|Alertas de inteligência de ameaça podem ficar mascarados|As regras de rede com o destino 80/443 para filtrar máscaras de saída alertam para ameaças de inteligência quando configuradas apenas para o modo de alerta.|Crie filtragem de saída para 80/443 utilizando as regras de aplicação. Ou, mude o modo de inteligência de ameaça para **Alerta e Negação**.|
|O DNAT do Azure Firewall não funciona para destinos IP privados|O suporte de ADN da Azure Firewall está limitado a saídas/entradas de Internet. O DNAT não funciona atualmente para destinos IP privados. Por exemplo, falou com a fala.|Esta é uma limitação atual.|
|Não é possível remover a primeira configuração ip pública|Cada endereço IP público do Azure Firewall é atribuído a uma *configuração IP*.  A primeira configuração IP é atribuída durante a implementação da firewall, e normalmente também contém uma referência à sub-rede de firewall (a menos que configurada explicitamente de forma diferente através de uma implementação do modelo). Não é possível eliminar esta configuração IP porque desafetaria a firewall. Ainda pode alterar ou remover o endereço IP público associado a esta configuração IP se a firewall tiver pelo menos um outro endereço IP público disponível para usar.|Esta ação é propositada.|
|As zonas de disponibilidade só podem ser configuradas durante a implantação.|As zonas de disponibilidade só podem ser configuradas durante a implantação. Não é possível configurar zonas de disponibilidade depois de uma firewall ter sido implantada.|Esta ação é propositada.|
|SNAT em ligações de entrada|Além do DNAT, as ligações através do endereço IP público de firewall (entrada) são SNATed a um dos IPs privados de firewall. Este requisito hoje (também para NVAs ativos/ativos) para garantir o encaminhamento simétrico.|Para preservar a fonte original para HTTP/S, considere a utilização de cabeçalhos [XFF.](https://en.wikipedia.org/wiki/X-Forwarded-For) Por exemplo, utilize um serviço como [Azure Front Door](../frontdoor/front-door-http-headers-protocol.md#front-door-to-backend) ou [Azure Application Gateway](../application-gateway/rewrite-http-headers.md) em frente à firewall. Também pode adicionar WAF como parte da Porta frontal Azure e corrente à firewall.
|Suporte de filtragem SQL FQDN apenas no modo proxy (porta 1433)|Para a base de dados Azure SQL, Azure Synapse Analytics e Azure SQL Gerenciado Instância:<br><br>A filtragem SQL FQDN é suportada apenas em modo proxy (porta 1433).<br><br>Para Azure SQL IaaS:<br><br>Se estiver a utilizar portas não standard, pode especificar essas portas nas regras de aplicação.|Para o SQL no modo de redireccionamento (o padrão se ligar a partir de Azure), pode, em vez disso, filtrar o acesso utilizando a etiqueta de serviço SQL como parte das regras da rede Azure Firewall.
|Não é permitido tráfego de saída na porta TCP 25| As ligações SMTP de saída que utilizam a porta TCP 25 estão bloqueadas. O Porto 25 é usado principalmente para a entrega de e-mail não autenticado. Este é o comportamento padrão da plataforma para máquinas virtuais. Para obter mais informações, consulte mais [problemas de conectividade SMTP de saída de resolução de problemas em Azure](../virtual-network/troubleshoot-outbound-smtp-connectivity.md). No entanto, ao contrário das máquinas virtuais, não é atualmente possível ativar esta funcionalidade no Azure Firewall. Nota: para permitir que o SMTP autenticado (porta 587) ou SMTP sobre uma porta com outra de 25, certifique-se de que configura uma regra de rede e não uma regra de aplicação, uma vez que a inspeção SMTP não é suportada neste momento.|Siga o método recomendado para enviar e-mail, conforme documentado no artigo de resolução de problemas do SMTP. Ou, exclua a máquina virtual que necessita de acesso SMTP de saída da sua rota padrão para a firewall. Em vez disso, configurar o acesso de saída diretamente para a internet.
|FTP ativo não é suportado|O FTP ativo é desativado no Azure Firewall para proteger contra ataques de ressalto FTP utilizando o comando FTP PORT.|Em vez disso, pode utilizar FTP passivo. Deve ainda abrir explicitamente as portas TCP 20 e 21 na firewall.
|Métrica de utilização do porto SNAT mostra 0%|A métrica de utilização da porta Azure Firewall SNAT pode mostrar uma utilização de 0% mesmo quando as portas SNAT são utilizadas. Neste caso, a utilização da métrica como parte da métrica de saúde da firewall proporciona um resultado incorreto.|Esta questão foi corrigida e a produção está prevista para maio de 2020. Em alguns casos, a redistribuição de firewall resolve o problema, mas não é consistente. Como uma solução intermédia, utilize apenas o estado de saúde da firewall para procurar *o estado=degradado* , não para *o status=insalubre*. A exaustão portuária mostrar-se-á *degradada.* *Não é saudável* para uso futuro quando são mais métricas para impactar a saúde da firewall.
|Dnat não é suportado com túnel forçado habilitado|As firewalls implantadas com túneis forçados ativados não podem suportar o acesso à entrada da Internet por causa do encaminhamento assimétrico.|Isto é por design devido ao encaminhamento assimétrico. O caminho de retorno para as ligações de entrada passa pela firewall no local, que não viu a ligação estabelecida.
|FtP passivo de saída pode não funcionar para Firewalls com vários endereços IP públicos, dependendo da configuração do servidor FTP.|A FTP passiva estabelece diferentes ligações para canais de controlo e dados. Quando uma Firewall com vários endereços IP públicos envia dados para fora, seleciona aleatoriamente um dos seus endereços IP públicos para o endereço IP de origem. O FTP pode falhar quando os canais de dados e de controlo utilizam diferentes endereços IP de origem, dependendo da configuração do servidor FTP.|Está prevista uma configuração SNAT explícita. Entretanto, pode configurar o seu servidor FTP para aceitar canais de dados e de controlo de diferentes endereços IP de origem (ver [exemplo para IIS).](https://docs.microsoft.com/iis/configuration/system.applicationhost/sites/sitedefaults/ftpserver/security/datachannelsecurity) Em alternativa, considere utilizar um único endereço IP nesta situação.|
|FTP passivo de entrada pode não funcionar dependendo da configuração do servidor FTP |A FTP passiva estabelece diferentes ligações para canais de controlo e dados. As ligações de entrada no Azure Firewall são SNATed para um dos endereços IP privados de firewall para garantir o encaminhamento simétrico. O FTP pode falhar quando os canais de dados e de controlo utilizam diferentes endereços IP de origem, dependendo da configuração do servidor FTP.|A preservação do endereço IP de origem original está a ser investigada. Entretanto, pode configurar o seu servidor FTP para aceitar canais de dados e controlo de diferentes endereços IP de origem.|
|A métrica NetworkRuleHit está a perder uma dimensão de protocolo|A métrica ApplicationRuleHit permite filtrar o protocolo baseado na filtragem, mas esta capacidade está em falta na métrica correspondente do NetworkRuleHit.|Uma correção está a ser investigada.|
|As regras da NAT com portas entre 64000 e 65535 não são apoiadas|O Azure Firewall permite que qualquer porta na gama 1-65535 nas regras de rede e aplicação, no entanto as regras DA APENAS suportam portas na gama 1-63999.|Esta é uma limitação atual.
|As atualizações de configuração podem demorar cinco minutos, em média,|Uma atualização de configuração do Azure Firewall pode demorar entre 3 a 5 minutos, em média, e as atualizações paralelas não são suportadas.|Uma correção está a ser investigada.|
|Azure Firewall usa cabeçalhos SNI TLS para filtrar tráfego HTTPS e MSSQL|Se o software do navegador ou do servidor não suportar a extensão do Indicador de Nome do Servidor (SNI), não será capaz de se ligar através do Azure Firewall.|Se o software do navegador ou do servidor não suportar SNI, então poderá ser capaz de controlar a ligação usando uma regra de rede em vez de uma regra de aplicação. Consulte [a indicação do nome do servidor](https://wikipedia.org/wiki/Server_Name_Indication) para software que suporta sNI.|
|DNS personalizado não funciona com túneis forçados|Se o túnel de força estiver ativado, o DNS personalizado não funciona.|Uma correção está a ser investigada.|
|Novo suporte público de endereço IP para múltiplas Zonas de Disponibilidade|Não é possível adicionar um novo endereço IP público quando implanta uma firewall com duas zonas de disponibilidade (1 e 2, 2 e 3, ou 1 e 3)|Trata-se de uma limitação de recursos de endereço IP público.|
|Start/Stop não funciona com uma firewall configurada em modo de túnel forçado|O arranque/paragem não funciona com firewall Azure configurado em modo de túnel forçado. Tentar iniciar a Firewall do Azure com túneis forçados configurados resulta no seguinte erro:<br><br>*Set-AzFirewall: AzureFirewall FW-xx management IP configuração IP não pode ser adicionada a uma firewall existente. Reimplantar com uma configuração IP de gestão se quiser utilizar suporte de túneis forçado. <br> StatusCode: 400 <br> ReasonPhrase: Mau pedido*|Sob investigação.<br><br>Como uma solução alternativa, pode eliminar a firewall existente e criar uma nova com os mesmos parâmetros.|
|Não é possível adicionar tags de política de firewall usando o portal|A Azure Firewall Policy tem uma limitação de suporte de patch que o impede de adicionar uma etiqueta usando o portal Azure. O seguinte erro é gerado: *Não foi possível guardar as etiquetas para o recurso*.|Uma correção está a ser investigada. Em alternativa, pode utilizar o cmdlet Azure PowerShell `Set-AzFirewallPolicy` para atualizar as tags.


## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Implementar e configurar o Azure Firewall com o portal do Azure](tutorial-firewall-deploy-portal.md)
- [Implementar o Azure Firewall através de um modelo](deploy-template.md)
- [Criar um ambiente de teste do Azure Firewall](scripts/sample-create-firewall-test.md)
