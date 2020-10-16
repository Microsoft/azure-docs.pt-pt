---
title: 'Azure ExpressRoute peering privado: Configure IPsec modo de transporte - anfitriões Windows'
description: Como permitir o modo de transporte IPsec entre VMs Azure Windows e no local, o Windows acolhe através do expressRoute private peering usando GPOs e OUs.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 10/17/2018
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 026b7ee6cf8061c7cff25b4f9f8d46b6ec3e6a8d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89396493"
---
# <a name="configure-ipsec-transport-mode-for-expressroute-private-peering"></a>Configure o modo de transporte IPsec para o espremiamento privado ExpressRoute

Este artigo ajuda-o a criar túneis IPsec em modo de transporte sobre expressRoute peering privado entre VMs Azure que executam Windows e anfitriões windows no local. Os passos deste artigo criam esta configuração utilizando objetos de política de grupo. Embora seja possível criar esta configuração sem utilizar unidades organizacionais (OUs) e objetos de política de grupo (GPOs), a combinação de OUs e GPOs ajudará a simplificar o controlo das suas políticas de segurança e permite-lhe aumentar rapidamente. Os passos deste artigo assumem que já tem uma configuração ative de Diretório e que está familiarizado com a utilização de OUs e GPOs.

## <a name="about-this-configuration"></a>Sobre esta configuração

A configuração nos seguintes passos utiliza uma única rede virtual Azure (VNet) com o espreguiçadeira privado ExpressRoute. No entanto, esta configuração pode abranger mais VNets Azure e redes no local. Este artigo irá ajudá-lo a definir uma política de encriptação IPsec, e aplicá-la a um grupo de VMs E anfitriões Azure no local que fazem parte da mesma U. Configura a encriptação entre os VMs Azure (vm1 e vm2) e os anfitriões no local apenas para tráfego HTTP com porta de destino 8080. Diferentes tipos de política IPsec podem ser criados com base nas suas necessidades.

### <a name="working-with-ous"></a>Trabalhar com OUs 

A política de segurança associada a uma UA é empurrada para os computadores via GPO. Algumas vantagens na utilização de OUs, em vez de aplicar políticas a um único hospedeiro, são:

* Associar uma política com um OU garante que os computadores que pertencem à mesma UO obtêm as mesmas políticas.
* A alteração da política de segurança associada à UO aplicará as alterações a todos os anfitriões da U.

### <a name="diagrams"></a>Diagramas

O diagrama seguinte mostra a interligação e o espaço de endereço IP atribuído. Os VMs Azure e o anfitrião no local estão a executar o Windows 2016. Os VMs Azure e os anfitriões no local 1 fazem parte do mesmo domínio. Os VMs Azure e os anfitriões no local podem resolver os nomes corretamente usando DNS.

[![11]][1]

Este diagrama mostra os túneis IPsec em trânsito em ExpressRoute private peering.

[![4]][4]

### <a name="working-with-ipsec-policy"></a>Trabalhar com a política do IPsec

No Windows, a encriptação está associada à política do IPsec. A política do IPsec determina qual o tráfego IP que é seguro e o mecanismo de segurança aplicado aos pacotes IP.
**As políticas IPSec** são compostas pelos seguintes itens: **Listas de Filtros,** **Ações de Filtragem**e **Regras de Segurança**.

Ao configurar a política do IPsec, é importante compreender a seguinte terminologia da política do IPsec:

* **Política do IPsec:** Uma coleção de regras. Apenas uma política pode ser ativa ("atribuída") em qualquer momento particular. Cada política pode ter uma ou mais regras, todas elas podem ser ativas simultaneamente. Um computador pode ser atribuído apenas uma política ativa do IPsec no momento dado. No entanto, dentro da política do IPsec, pode definir múltiplas ações que podem ser tomadas em diferentes situações. Cada conjunto de regras IPsec está associado a uma lista de filtros que afeta o tipo de tráfego de rede a que a regra se aplica.

* **Listas de filtros:** As listas de filtros são um pacote de um ou mais filtros. Uma lista pode conter vários filtros. O filtro define se a comunicação é permitida, segura ou bloqueada, de acordo com as gamas de endereços IP, protocolos ou mesmo portas de protocolo específicas. Cada filtro corresponde a um conjunto particular de condições; por exemplo, pacotes enviados de uma determinada sub-rede para um determinado computador numa porta de destino específica. Quando as condições de rede correspondem a um ou mais desses filtros, a lista de filtros é ativada. Cada filtro é definido dentro de uma lista de filtros específica. Os filtros não podem ser partilhados entre listas de filtros. No entanto, uma determinada lista de filtros pode ser incorporada em várias políticas de IPsec. 

* **Ações de filtragem:** Um método de segurança define um conjunto de algoritmos de segurança, protocolos e ofertas de computador durante as negociações do IKE. As ações de filtragem são listas de métodos de segurança, classificados por ordem de preferência.  Quando um computador negoceia uma sessão do IPsec, aceita ou envia propostas com base na definição de segurança armazenada na lista de ações de filtro.

* **Regras de segurança:** As regras regem como e quando uma política do IPsec protege a comunicação. Utiliza **a lista de filtros** e **ações de filtragem** para criar uma regra IPsec para construir a ligação IPsec. Cada política pode ter uma ou mais regras, todas elas podem ser ativas simultaneamente. Cada regra contém uma lista de filtros IP e uma coleção de ações de segurança que ocorrem em uma correspondência com a lista de filtros:
  * Ações de filtro IP
  * Métodos de autenticação
  * Definições de túneis IP
  * Tipos de ligação

[![5]][5]

## <a name="before-you-begin"></a>Antes de começar

Certifique-se de que cumpre os seguintes requisitos:

* Tem de ter uma configuração de Diretório Ativo funcional que pode utilizar para implementar as definições de Política de Grupo. Para obter mais informações sobre GPOs, consulte [Objetos de Política de Grupo.](https://msdn.microsoft.com/library/windows/desktop/aa374162(v=vs.85).aspx)

* Deve ter um circuito ExpressRoute ativo.
  * Para obter informações sobre a criação de um circuito ExpressRoute, consulte [Criar um circuito ExpressRoute](expressroute-howto-circuit-arm.md). 
  * Verifique se o circuito está ativado pelo seu fornecedor de conectividade. 
  * Verifique se tem o Azure a espreitar para o seu circuito. Consulte o artigo [de encaminhamento de configuração](expressroute-howto-routing-arm.md) para obter instruções de encaminhamento. 
  * Verifique se tem um VNet e uma porta de rede virtual criada e totalmente a provisionada. Siga as instruções para [criar uma porta de rede virtual para o ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Um gateway de rede virtual para ExpressRoute utiliza o GatewayType 'ExpressRoute', não VPN.

* O gateway de rede virtual ExpressRoute deve ser ligado ao circuito ExpressRoute. Para obter mais informações, consulte [a Ligação de um VNet a um circuito ExpressRoute](expressroute-howto-linkvnet-arm.md).

* Verifique se os VM do Azure Windows estão implantados no VNet.

* Verifique se existe conectividade entre os anfitriões no local e os VMs Azure.

* Verifique se os VMs do Azure Windows e os anfitriões no local são capazes de utilizar DNS para resolver corretamente os nomes.

### <a name="workflow"></a>Fluxo de trabalho

1. Crie um GPO e associe-o à U.
2. Defina uma **ação de filtro**IPsec .
3. Defina uma lista de **filtros**IPsec .
4. Criar uma Política IPsec com **regras de segurança.**
5. Atribua o GPO do IPsec à U.

### <a name="example-values"></a>Valores de exemplo

* **Nome do domínio:** ipsectest.com

* **OU:** IPSecou

* **Computador Windows no local:** anfitrião1

* **VMs Azure Windows:** vm1, vm2

## <a name="1-create-a-gpo"></a><a name="creategpo"></a>1. Criar um GPO

1. Para criar um novo GPO ligado a um OU, abra o snap-in de Gestão de Políticas de Grupo e localize o UO ao qual o GPO estará ligado. No exemplo, a Uo chama-se **IPSecou**. 

   [![9]][9]
2. No snap-in de Gestão de Políticas de Grupo, selecione o OU e clique à direita. No dropdown, clique "**Criar um GPO neste domínio, e link-lo aqui...**".

   [![10]][10]
3. Nomeie o GPO como um nome intuitivo para que possa localizá-lo facilmente mais tarde. Clique **em OK** para criar e ligar o GPO.

   [![11]][11]

## <a name="2-enable-the-gpo-link"></a><a name="enablelink"></a>2. Ativar a ligação GPO

Para aplicar o GPO à U, o GPO não só deve estar ligado à UO, como também deve ser ativado.

1. Localize o GPO que criou, clique à direita e **selecione Editar** a partir do dropdown.
2. Para aplicar o GPO no OU, selecione **Link Enabled**.

   [![12]][12]

## <a name="3-define-the-ip-filter-action"></a><a name="filteraction"></a>3. Definir a ação do filtro IP

1. A partir da política de segurança IP drop-down, clique à direita **no Diretório Ativo,** e, em seguida, clique em **Gerir listas de filtros IP e ações de filtragem...**.

   [![15]][15]
2. No separador "**Gerir ações de filtro",** clique em **Adicionar**.

   [![16]][16]

3. No **assistente de ação do filtro de segurança IP**, clique em **Seguinte**.

   [![17]][17]
4. Nomeie a ação do filtro como nome intuitivo para que possa encontrá-lo mais tarde. Neste exemplo, a ação do filtro chama-se **myEncrypation**. Também pode adicionar uma descrição. Em seguida, clique **em Seguinte**.

   [![18]][18]
5. **Negociar a segurança** permite-lhe definir o comportamento se o IPsec não puder ser estabelecido com outro computador. **Selecione Negociar a segurança**e clicar em **Seguinte**.

   [![19]][19]
6. Na **Comunicação com computadores que não suportam** a página IPsec, selecione **Não permita comunicação não garantida,** em seguida, clique em **Seguinte**.

   [![20]][20]
7. Na página **IP Tráfego e Segurança,** selecione **'Personal'** e, em seguida, clique em **Definições...**.

   [![21]][21]
8. Na página De Definições do **Método de Segurança Personalizada,** selecione a **integridade e encriptação dos dados (ESP): SHA1, 3DES**. Em seguida, clique **OK**.

   [![22]][22]
9. Na página **'Manage Filter Actions',** pode ver que o filtro **myEncrypation** foi adicionado com sucesso. Clique em **Close** (Fechar).

   [![23]][23]

## <a name="4-define-an-ip-filter-list"></a><a name="filterlist1"></a>4. Definir uma lista de filtros IP

Crie uma lista de filtros que especifique o tráfego HTTP encriptado com a porta de destino 8080.

1. Para qualificar quais os tipos de tráfego que devem ser encriptados, utilize uma **lista de filtros IP**. No **separador 'Gestão de Listas de Filtros IP',** clique em **Adicionar** para adicionar uma nova lista de filtros IP.

   [![24]][24]
2. No **campo Nome:** campo, digite um nome para a sua lista de filtros IP. Por exemplo, **azure-onpremises-HTTP8080**. Em seguida, clique em **Adicionar**.

   [![25]][25]
3. Na **página de descrição do filtro IP e página de propriedade espelhada,** selecione **Mirrored**. A definição espelhada corresponde a pacotes que vão em ambas as direções, o que permite uma comunicação bidirecional. Em seguida, clique em **Seguinte**.

   [![26]][26]
4. Na página **IP Traffic Source,** a partir do **endereço 'Fonte:** dropdown,' escolha **Um endereço IP específico ou sub-rede**. 

   [![27]][27]
5. Especifique o endereço de origem **endereço IP Endereço ou Sub-rede:** do tráfego IP e, em seguida, clique em **Seguinte**.

   [![28]][28]
6. Especifique o **endereço destino:** Endereço IP ou Sub-rede. Em seguida, clique **em Seguinte**.

   [![29]][29]
7. Na página **ip Protocol Type,** selecione **TCP**. Em seguida, clique **em Seguinte**.

   [![30]][30]
8. Na página **porta do protocolo IP,** selecione **De qualquer porta** e para **esta porta:**. Tipo **8080** na caixa de texto. Estas definições especificam apenas o tráfego HTTP na porta de destino 8080 será encriptado. Em seguida, clique **em Seguinte**.

   [![31]][31]
9. Veja a lista de filtros IP.  A configuração da Lista de Filtros IP **azure-onpremises-HTTP8080** desencadeia encriptação para todo o tráfego que corresponda aos seguintes critérios:

   * Qualquer endereço de origem em 10.0.1.0/24 (Subneta Azure)
   * Qualquer endereço de destino em 10.2.27.0/25 (sub-rede no local)
   * Protocolo TCP
   * Porto de destino 8080

   [![32]][32]

## <a name="5-edit-the-ip-filter-list"></a><a name="filterlist2"></a>5. Editar a lista de filtros IP

Para encriptar o mesmo tipo de tráfego na direção oposta (desde o anfitrião no local até ao Azure VM) precisa de um segundo filtro IP. O processo de configuração do novo filtro é o mesmo que usou para configurar o primeiro filtro IP. As únicas diferenças são a sub-rede de origem e a sub-rede de destino.

1. Para adicionar um novo filtro IP à Lista de Filtros IP, **selecione Editar**.

   [![33]][33]
2. Na página **lista de filtros IP,** clique em **Adicionar**.

   [![34]][34]
3. Criar um segundo filtro IP utilizando as definições no seguinte exemplo:

   [![35]][35]
4. Depois de criar o segundo filtro IP, a lista de filtros IP será assim:

   [![36]][36]

Se for necessária encriptação entre uma localização no local e uma sub-rede Azure para proteger uma aplicação, em vez de modificar a lista de filtros IP existente, pode adicionar uma nova lista de filtros IP. Associar 2 listas de filtros IP à mesma política IPsec proporciona uma melhor flexibilidade porque uma lista específica de filtros IP pode ser modificada ou removida a qualquer momento sem afetar as outras listas de filtros IP.

## <a name="6-create-an-ipsec-security-policy"></a><a name="ipsecpolicy"></a>6. Criar uma política de segurança IPsec 

Criar uma política do IPsec com regras de segurança.

1. Selecione as **Políticas de Segurança IP no diretório ativo** que está associada à U. Clique com o botão direito e selecione **Criar Política de Segurança IP**.

   [![37]][37]
2. Diga o nome da política de segurança. Por exemplo, **as políticas-azure-onpremises**. Em seguida, clique **em Seguinte**.

   [![38]][38]
3. Clique **em seguida** sem selecionar a caixa de verificação.

   [![39]][39]
4. Verifique se a caixa de verificação **de propriedades editar** está selecionada e, em seguida, clique em **Terminar**.

   [![40]][40]

## <a name="7-edit-the-ipsec-security-policy"></a><a name="editipsec"></a>7. Editar a política de segurança do IPsec

Adicione à política IPsec a **Lista de Filtros IP** e a Ação de **Filtragem** que configuraste anteriormente.

1. No separador HTTP Policy Properties **Rules,** clique em **Adicionar**.

   [![41]][41]
2. Na página Bem-vindo, clique em **Seguinte**.

   [![42]][42]
3. Uma regra oferece a opção de definir o modo IPsec: modo de túnel ou modo de transporte.

   * No modo túnel, o pacote original é encapsulado por um conjunto de cabeçalhos IP. O modo túnel protege as informações internas de encaminhamento encriptando o cabeçalho IP do pacote original. O modo túnel é amplamente implementado entre gateways em cenários VPN local-to-site. O modo túnel é na maioria dos casos usado para encriptação de ponta a ponta entre os anfitriões.

   * O modo de transporte encripta apenas a carga útil e o reboque ESP; o cabeçalho IP do pacote original não está encriptado. No modo de transporte, a fonte IP e o destino IP dos pacotes são inalterados.

   Selecione **Esta regra não especifica um túnel**e, em seguida, clique em **Seguinte**.

   [![43]][43]
4. **O Tipo de Rede** define qual a ligação de rede associada à política de segurança. Selecione **Todas as ligações de rede**e, em seguida, clique em **Seguinte**.

   [![44]][44]
5. Selecione a lista de filtros IP que criou anteriormente,  **azure-onpremises-HTTP8080**, e, em seguida, clique em **Seguinte**.

   [![45]][45]
6. Selecione a **myEncryption** de Ação filtrante existente que criou anteriormente.

   [![46]][46]
7. O Windows suporta quatro tipos distintos de autenticações: Kerberos, certificados, NTLMv2 e chave pré-partilhada. Como estamos a trabalhar com anfitriões ligados ao domínio, selecione **Ative Directory padrão (protocolo Kerberos V5)** e, em seguida, clique em **Seguinte**.

   [![47]][47]
8. A nova política cria a regra de segurança: **azure-onpremises-HTTP8080**. Clique em **OK**.

   [![48]][48]

A política do IPsec requer que todas as ligações HTTP na porta de destino 8080 utilizem o modo de transporte IPsec. Porque HTTP é um protocolo de texto claro, ter a política de segurança ativada garante que os dados são encriptados quando são transferidos através do peering privado ExpressRoute. A política de segurança IP para o Ative Directory é mais complexa para configurar do que o Windows Firewall com Segurança Avançada, mas permite uma maior personalização da ligação IPsec.

## <a name="8-assign-the-ipsec-gpo-to-the-ou"></a><a name="assigngpo"></a>8. Atribuir o GPO do IPsec à UO

1. Veja a política. A política do grupo de segurança está definida, mas ainda não foi atribuída.

   [![49]][49]
2. Para atribuir a política do grupo de segurança ao **OU IPSecOU,** clique à direita na política de segurança e escolha **Atribuir**.
   Todos os computadores pertencem à UO terão a política do grupo de segurança atribuída.

   [![50]][50]

## <a name="check-traffic-encryption"></a><a name="checktraffic"></a>Verifique a encriptação do tráfego

Para verificar a encriptação GPO aplicada no OU, instale o IIS em todos os VMs Azure e no anfitrião1. Cada IIS é personalizado para responder a pedidos HTTP na porta 8080.
Para verificar a encriptação, pode instalar um farejador de rede (como o Wireshark) em todos os computadores da U.
Um script powershell funciona como um cliente HTTP para gerar pedidos HTTP na porta 8080:

```powershell
$url = "http://10.0.1.20:8080"
while ($true) {
try {
[net.httpWebRequest]
$req = [net.webRequest]::create($url)
$req.method = "GET"
$req.ContentType = "application/x-www-form-urlencoded"
$req.TimeOut = 60000

$start = get-date
[net.httpWebResponse] $res = $req.getResponse()
$timetaken = ((get-date) - $start).TotalMilliseconds

Write-Output $res.Content
Write-Output ("{0} {1} {2}" -f (get-date), $res.StatusCode.value__, $timetaken)
$req = $null
$res.Close()
$res = $null
} catch [Exception] {
Write-Output ("{0} {1}" -f (get-date), $_.ToString())
}
$req = $null

# uncomment the line below and change the wait time to add a pause between requests
#Start-Sleep -Seconds 1
}

```
A seguinte captura de rede mostra os resultados do anfitrião no local1 com o filtro de visualização ESP para corresponder apenas ao tráfego encriptado:

[![51]][51]

Se executar o script powershell on-premisies (cliente HTTP), a captura de rede no Azure VM mostra um traço semelhante.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o ExpressRoute, consulte as [FAQ ExpressRoute.](expressroute-faqs.md)

<!--Image References-->

[1]: ./media/expressroute-howto-ipsec-transport-private-windows/network-diagram.png "rede Diagrama IPsec modo de transporte através do ExpressRoute"
[4]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-interesting-traffic.png "IPsec tráfego interessante"
Política "IPsec do Windows" [5]: ./media/expressroute-howto-ipsec-transport-private-windows/windows-ipsec.png
[9]: ./media/expressroute-howto-ipsec-transport-private-windows/ou.png "Unidade de Organização na Política de Grupo"
[10]: ./media/expressroute-howto-ipsec-transport-private-windows/create-gpo-ou.png "criar um GPO associado à UO"
[11]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-name.png "atribuir um nome à GPO associada à UO"
[12]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-gpo.png "editar o GPO"
[15]: ./media/expressroute-howto-ipsec-transport-private-windows/manage-ip-filter-list-filter-actions.png "Gerir listas de filtros IP e ações de filtragem"
[16]: ./media/expressroute-howto-ipsec-transport-private-windows/add-filter-action.png "adicionar Ação de Filtro"
[17]: ./media/expressroute-howto-ipsec-transport-private-windows/action-wizard.png "Assistente de Ação"
[18]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-name.png "Nome de ação do filtro"
[19]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action.png "Ação de Filtro"
[20]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-no-ipsec.png "especificar que o comportamento é uma ligação insegura é estabelecida"
[21]: ./media/expressroute-howto-ipsec-transport-private-windows/security-method.png "mecanismo de segurança"
[22]: ./media/expressroute-howto-ipsec-transport-private-windows/custom-security-method.png "Método de segurança personalizado"
[23]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-actions-list.png "lista de ação de filtro"
[24]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-ip-filter.png "Adicione uma nova lista de filtros IP"
[25]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-http-traffic.png "Adicione tráfego HTTP ao filtro IP"
[Pacote de jogo 26]: ./media/expressroute-howto-ipsec-transport-private-windows/match-both-direction.png "em ambas as direções"
[27]: ./media/expressroute-howto-ipsec-transport-private-windows/source-address.png "seleção da sub-rede Fonte"
[28]: ./media/expressroute-howto-ipsec-transport-private-windows/source-network.png "Rede de Fontes"
[Rede de Destino 29]: ./media/expressroute-howto-ipsec-transport-private-windows/destination-network.png "Destination Network"
[30]: ./media/expressroute-howto-ipsec-transport-private-windows/protocol.png "Protocolo"
[31]: ./media/expressroute-howto-ipsec-transport-private-windows/source-port-and-destination-port.png "porto de origem e porto de destino"
[32]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list.png "lista de filtros"
Lista de "filtros IP" de [33]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-for-http.pngIP com tráfego HTTP
[34]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-add-second-entry.png "Adicionar um segundo filtro IP"
[35]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-second-entry.png "IP Filter lista-segunda entrada"
[36]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list-2entries.png "lista de filtro IP segundo entrada"
[37]: ./media/expressroute-howto-ipsec-transport-private-windows/create-ip-security-policy.png "criar a política de segurança IP"
[38]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-policy-name.png "nome da política do IPsec"
[39]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-wizard.png "Assistente de política do IPsec"
[40]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-security-policy.png "edição da política do IPsec"
[41]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-rule.png "adicionar nova regra de segurança à política do IPsec"
[42]: ./media/expressroute-howto-ipsec-transport-private-windows/create-security-rule.png "criar uma nova regra de segurança"
[43]: ./media/expressroute-howto-ipsec-transport-private-windows/transport-mode.png "Modo de Transporte"
[44]: ./media/expressroute-howto-ipsec-transport-private-windows/network-type.png "Tipo de rede"
[45]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-list.png "seleção da lista de filtros IP existente"
[46]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-action.png "seleção da ação filtrante existente"
[47]: ./media/expressroute-howto-ipsec-transport-private-windows/authentication-method.png "seleção do método de autenticação"
[48]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-completed.png "fim do processo de criação da política de segurança"
[49]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-not-assigned.png "Política do IPsec ligada à GPO, mas não atribuída"
[50]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-assigned.png "Política IPsec atribuída à GPO"
[51]: ./media/expressroute-howto-ipsec-transport-private-windows/encrypted-traffic.png "Captura de tráfego encriptado IPsec"
