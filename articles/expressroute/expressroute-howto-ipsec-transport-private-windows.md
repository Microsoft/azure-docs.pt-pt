---
title: 'Configure o modo de transporte IPsec para o peering de privada anfitriões do Windows: ExpressRoute: Azure | Microsoft Docs'
description: Como ativar o modo de transporte IPsec entre o Azure Windows VMs e anfitriões do Windows no local através do ExpressRoute privada de peering através de GPOs e UOs.
services: expressroute
author: fabferri
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: fabferri
ms.custom: seodec18
ms.openlocfilehash: d728980517988e2dc39be4e4b64d20157a1aef54
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58121184"
---
# <a name="configure-ipsec-transport-mode-for-expressroute-private-peering"></a>Configurar o modo de transporte IPsec para peering privado do ExpressRoute

Este artigo ajuda-o a criar túneis IPsec em modo de transporte através do ExpressRoute privada para o peering entre as VMs do Azure a executar o Windows e anfitriões do Windows no local. Os passos neste artigo criam esta configuração com objetos de política de grupo. Embora seja possível criar esta configuração sem utilizar unidades organizacionais (UOs) e (GPOs) de objetos de política de grupo, a combinação de unidades organizacionais e GPOs irá ajudar a simplificar o controle das suas políticas de segurança e permite-lhe dimensionar rapidamente cópia de segurança. Os passos neste artigo pressupõem que já tem uma configuração do Active Directory e que está familiarizado com o uso de unidades organizacionais e GPOs.

## <a name="about-this-configuration"></a>Sobre esta configuração

A configuração nos passos seguintes utilizar uma rede virtual do Azure única (VNet) com o peering privado do ExpressRoute. No entanto, esta configuração pode abranger mais VNets do Azure e redes no local. Este artigo ajuda-o a definir uma política de encriptação de IPsec e aplicá-la a um grupo de anfitriões e VMs do Azure no local que fazem parte da mesma UO. Configurar a encriptação de entre as VMs do Azure (vm1 e vm2) e o host1 no local apenas para tráfego HTTP com a porta de destino 8080. Diferentes tipos de IPsec política pode ser criada com base nos seus requisitos.

### <a name="working-with-ous"></a>Trabalhar com unidades organizacionais 

A política de segurança associada uma UO é enviada para os computadores através de GPO. Algumas vantagens em utilizar unidades organizacionais, em vez de aplicar políticas para um único host, são:

* Associar uma política uma UO garante que os computadores que pertencem à mesma UO obtêm as mesmas políticas.
* A alteração da política de segurança associada a UO, as alterações serão aplicadas a todos os anfitriões na UO.

### <a name="diagrams"></a>Diagramas

O diagrama seguinte mostra a interconexão e o espaço de endereços IP atribuído. As VMs do Azure e o anfitrião no local estão a executar o Windows 2016. As VMs do Azure e o host1 no local são parte do mesmo domínio. As VMs do Azure e os anfitriões no local, podem resolver nomes corretamente através de DNS.

[![1]][1]

Este diagrama mostra os túneis IPsec em trânsito no peering privado do ExpressRoute.

[![4]][4]

### <a name="working-with-ipsec-policy"></a>Trabalhar com a política de IPsec

No Windows, a encriptação está associada a política de IPsec. Política de IPsec determina qual o tráfego IP está protegido e o mecanismo de segurança aplicadas a pacotes IP.
**Políticas IPSec** são compostos dos seguintes itens: **Filtrar as listas**, **filtrar ações**, e **regras de segurança**.

Ao configurar a política de IPsec, é importante perceber a terminologia de política de IPsec seguinte:

* **Política de IPsec:** Uma coleção de regras. Apenas uma política pode estar ativa ("atribuído") em qualquer momento específico. Cada política pode ter uma ou mais regras, as quais podem estar ativas em simultâneo. Pode ser atribuído a um computador apenas uma política de IPsec Active Directory em dado momento. No entanto, na diretiva de IPsec, pode definir várias ações que podem ser realizadas em situações diferentes. Cada conjunto de regras de IPsec está associado uma lista de filtros que afeta o tipo de tráfego de rede às quais se aplica a regra.

* **Listas de filtros:** Listas de filtros são o pacote de um ou mais filtros. Uma lista pode conter vários filtros. Filtro define se é permitida, protegida ou bloqueada, de acordo com os intervalos de endereços IP, protocolos ou portas de protocolo específico até mesmo a comunicação. Cada filtro corresponde a um determinado conjunto de condições; Por exemplo, pacotes enviados a partir de uma sub-rede específica para um determinado computador numa porta de destino específico. Quando as condições de rede correspondem a um ou mais desses filtros, a lista de filtro é ativada. Cada filtro é definido dentro de uma lista de filtros específicos. Filtros não podem ser partilhados entre as listas de filtro. No entanto, uma lista de filtros de determinado pode ser incorporada ao várias políticas de IPsec. 

* **Filtre ações:** Um método de segurança define um conjunto de algoritmos de segurança, protocolos, e oferece um computador chave durante negociações de IKE. As ações de filtro são listas de métodos de segurança, ordenados por ordem de preferência.  Quando um computador negocia uma sessão de IPsec, ela aceita ou envia propostas com base na definição de segurança armazenada na lista de ações de filtro.

* **Regras de segurança:** Regras regem como e quando uma política de IPsec protege a comunicação. Ele usa **a lista de filtros** e **filtrar ações** para criar uma regra de IPsec para criar a ligação IPsec. Cada política pode ter uma ou mais regras, as quais podem estar ativas em simultâneo. Cada regra contém uma lista de filtros IP e uma coleção de ações de segurança que ocorrem após uma correspondência com essa lista de filtros:
  * Ações de filtro IP
  * Métodos de autenticação
  * Definições de túnel IP
  * Tipos de ligação

[![5]][5]

## <a name="before-you-begin"></a>Antes de começar

Certifique-se de que cumpre os seguintes pré-requisitos:

* Tem de ter uma configuração do Active Directory está a funcionar, que pode utilizar para implementar definições de política de grupo. Para obter mais informações sobre GPOs, consulte [objetos de política de grupo](https://msdn.microsoft.com/library/windows/desktop/aa374162(v=vs.85).aspx).

* Deve ter um circuito ExpressRoute ativo.
  * Para obter informações sobre a criação de um circuito do ExpressRoute, veja [criar um circuito do ExpressRoute](expressroute-howto-circuit-arm.md). 
  * Certifique-se de que o circuito está ativado pelo seu fornecedor de conectividade. 
  * Certifique-se de que tem o peering privado do Azure configurado para o seu circuito. Consulte a [configurar o encaminhamento](expressroute-howto-routing-arm.md) artigo para obter instruções de encaminhamento. 
  * Certifique-se de que tem uma VNet e um gateway de rede virtual criada e totalmente aprovisionado. Siga as instruções para [criar um gateway de rede virtual para o ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Um gateway de rede virtual para o ExpressRoute utiliza o GatewayType "ExpressRoute", não VPN.

* O gateway de rede virtual do ExpressRoute tem de estar ligado ao circuito do ExpressRoute. Para obter mais informações, consulte [ligar uma rede virtual a um circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md).

* Certifique-se de que as VMs do Windows Azure são implementadas para a VNet.

* Certifique-se de que existe conectividade entre os anfitriões no local e as VMs do Azure.

* Certifique-se de que as VMs do Windows Azure e os anfitriões no local são capazes de utilizar o DNS para resolver corretamente os nomes.

### <a name="workflow"></a>Fluxo de trabalho

1. Criar um GPO e associá-la para a UO.
2. Definir um IPsec **filtrar ação**.
3. Definir um IPsec **filtrar lista**.
4. Criar uma política de IPsec com **regras de segurança**.
5. Atribua o GPO de IPsec para a UO.

### <a name="example-values"></a>Valores de exemplo

* **Nome de domínio:** ipsectest.com

* **OU:** IPSecOU

* **Computador com Windows no local:** host1

* **VMs do Windows Azure:** vm1, vm2

## <a name="creategpo"></a>1. Criar um GPO

1. Para criar um novo GPO vinculado a uma UO, abra o snap-in gerenciamento de diretiva de grupo e localize a UO para os quais o GPO será ligado. No exemplo, é com o nome do UO **IPSecOU**. 

   [![9]][9]
2. No snap-in gerenciamento de diretiva de grupo, selecione a unidade Organizacional e, com o botão direito. Na lista pendente, clique em "**criar um GPO neste domínio e ligá-lo aqui...** ".

   [![10]][10]
3. Nome do GPO um nome intuitivo, de modo a que pode facilmente localizá-la mais tarde. Clique em **OK** para criar e vincular o GPO.

   [![11]][11]

## <a name="enablelink"></a>2. Ativar a ligação de GPO

Para aplicar o GPO à UO, o GPO deve não apenas ser anexado à UO, mas a ligação tem de estar também ativada.

1. Localizar o GPO que criou, botão direito do mouse e selecione **editar** na lista pendente.
2. Para aplicar o GPO à UO, selecione **ligação ativada**.

   [![12]][12]

## <a name="filteraction"></a>3. Definir a ação de filtro IP

1. A lista pendente, com o botão direito **política de segurança de IP no Active Directory**e, em seguida, clique em **IP gerir listas de filtrar e filtrar ações...** .

   [![15]][15]
2. Sobre a "**filtro de gerir ações**", clique em **adicionar**.

   [![16]][16]

3. Sobre o **Assistente de ação de filtro de segurança de IP**, clique em **próxima**.

   [![17]][17]
4. Nomeie a ação de filtro um nome intuitivo para que pode encontrá-lo mais tarde. Neste exemplo, a ação de filtro é denominada **myEncryption**. Também pode adicionar uma descrição. Clique depois em **Seguinte**.

   [![18]][18]
5. **Negociar segurança** permite-lhe definir o comportamento se o IPsec não é possível estabelecer com outro computador. Selecione **negociar segurança**, em seguida, clique em **próxima**.

   [![19]][19]
6. No **comunicação com computadores que não suportam IPsec** página, selecione **não permitir a comunicação protegida**, em seguida, clique em **seguinte**.

   [![20]][20]
7. No **tráfego de IP e a segurança** página, selecione **personalizada**, em seguida, clique em **definições...** .

   [![21]][21]
8. Sobre o **definições de segurança personalizadas do método** página, selecione **integridade dos dados e de encriptação (ESP): SHA1, 3DES**. Em seguida, clique em **OK**.

   [![22]][22]
9. Sobre o **gerir ações de filtro** página, pode ver que o **myEncryption** filtro foi adicionado com êxito. Clique em **Fechar**.

   [![23]][23]

## <a name="filterlist1"></a>4. Definir uma lista de filtros IP

Crie uma lista de filtro que especifica o tráfego HTTP criptografado com a porta de destino 8080.

1. Para qualificar quais tipos de tráfego têm de ser encriptados, utilize um **lista de filtros IP**. Na **gerir a lista de filtro de IP** separador, clique em **Add** para adicionar uma nova lista de filtro IP.

   [![24]][24]
2. Na **Name:** , digite um nome para a lista de filtros IP. Por exemplo, **azure-onpremises-HTTP8080**. Em seguida, clique em **adicionar**.

   [![25]][25]
3. Sobre o **propriedade de descrição do filtro de IP e espelhados** página, selecione **espelhados**. A definição espelhada corresponde a pacotes em ambas as direções, que permite a comunicação bidirecional. Clique depois em **Seguinte**.

   [![26]][26]
4. No **IP de origem de tráfego** página, da **endereço de origem:** menu pendente, escolha **um endereço IP ou sub-rede específica**. 

   [![27]][27]
5. Especifique o endereço de origem **endereço IP ou sub-rede:** o tráfego de IP, em seguida, clique em **próxima**.

   [![28]][28]
6. Especifique o **endereço de destino:** Endereço IP ou sub-rede. Clique depois em **Seguinte**.

   [![29]][29]
7. Sobre o **tipo de protocolo IP** página, selecione **TCP**. Clique depois em **Seguinte**.

   [![30]][30]
8. Sobre o **porta de protocolo IP** página, selecione **em qualquer porta** e **para esta porta:**. Tipo **8080** na caixa de texto. Estas definições especificam que apenas o tráfego HTTP na porta de destino 8080 será encriptado. Clique depois em **Seguinte**.

   [![31]][31]
9. Ver a lista de filtro IP.  A configuração da lista de filtro de IP **azure-onpremises-HTTP8080** aciona a encriptação de todo o tráfego que corresponde aos critérios seguintes:

   * Qualquer endereço de origem no 10.0.1.0/24 (Subnet2 do Azure)
   * Qualquer endereço de destino no 10.2.27.0/25 (sub-rede no local)
   * Protocolo TCP
   * Porta de destino 8080

   [![32]][32]

## <a name="filterlist2"></a>5. Editar a lista de filtro IP

Para encriptar o mesmo tipo de tráfego na direção oposta (do host no local para a VM do Azure), é necessário um segundo filtro IP. O processo de configuração do novo filtro é o mesmo processo que utilizou para configurar o primeiro filtro IP. As únicas diferenças estão a sub-rede de origem e a sub-rede de destino.

1. Para adicionar um novo filtro IP para a lista de filtros de IP, selecione **editar**.

   [![33]][33]
2. Sobre o **lista de filtros de IP** página, clique em **Add**.

   [![34]][34]
3. Crie um segundo filtro IP utilizando as definições no exemplo a seguir:

   [![35]][35]
4. Depois de criar o segundo filtro IP, a lista de filtros IP terá o seguinte aspeto:

   [![36]][36]

Se a encriptação é necessária entre uma localização no local e uma sub-rede do Azure para proteger uma aplicação, em vez de modificar a lista de filtro IP existente, pode adicionar uma nova lista de filtro IP em vez disso. Associar o 2 IP listas de filtros para a mesma política de IPsec fornece mais flexibilidade porque uma lista de filtro IP específica pode ser modificada ou removida em qualquer altura, sem afetar as outras listas de filtro IP.

## <a name="ipsecpolicy"></a>6. Criar uma política de segurança do IPsec 

Crie uma política de IPsec com regras de segurança.

1. Selecione o **IPSecurity políticas no Active directory** que está associado a UO. Com o botão direito e selecione **criar política de segurança de IP**.

   [![37]][37]
2. Atribuir nome à política de segurança. Por exemplo, **política-azure-onpremises**. Clique depois em **Seguinte**.

   [![38]][38]
3. Clique em **seguinte** sem selecionar a caixa de verificação.

   [![39]][39]
4. Certifique-se de que o **editar propriedades** caixa de verificação está selecionada e, em seguida, clique em **concluir**.

   [![40]][40]

## <a name="editipsec"></a>7. Editar a política de segurança do IPsec

Adicionar a política de IPsec a **lista de filtros de IP** e **filtro de ação** que configurou anteriormente.

1. A diretiva de HTTP propriedades **regras** separador, clique em **Add**.

   [![41]][41]
2. Na página de boas-vindas, clique em **seguinte**.

   [![42]][42]
3. Uma regra fornece a opção de definir o modo de IPsec: modo de túnel ou modo de transporte.

   * No modo de túnel, o pacote original é encapsulado por um conjunto de cabeçalhos IP. Modo de encapsulamento protege as informações de encaminhamento internas ao encriptar o cabeçalho IP do pacote original. Modo de encapsulamento é amplamente implementado entre gateways em cenários VPN de site a site. Modo de encapsulamento é na maioria dos casos, utilizadas para encriptação de ponto a ponto entre anfitriões.

   * Modo de transporte encripta apenas o payload e o trailer do ESP; o cabeçalho IP do pacote original não está encriptado. No modo de transporte, o IP de origem e destino IP dos pacotes permanecem inalteradas.

   Selecione **esta regra não especifica um túnel**e, em seguida, clique em **próxima**.

   [![43]][43]
4. **Tipo de rede** define quais associates de ligação com a política de segurança de rede. Selecione **todas as ligações de rede**e, em seguida, clique em **próxima**.

   [![44]][44]
5. Selecione a lista de filtro IP que criou anteriormente, **azure-onpremises-HTTP8080**e, em seguida, clique em **próxima**.

   [![45]][45]
6. Selecione a ação de filtro existente **myEncryption** que criou anteriormente.

   [![46]][46]
7. Windows oferece suporte a quatro tipos de autenticações diferentes: Kerberos, certificados, NTLMv2 e chave pré-partilhada. Uma vez que estamos a trabalhar com anfitriões associados a um domínio, selecione **padrão do Active Directory (protocolo Kerberos V5)** e, em seguida, clique em **próxima**.

   [![47]][47]
8. A nova política cria a regra de segurança: **azure-onpremises-HTTP8080**. Clique em **OK**.

   [![48]][48]

A diretiva IPsec exija todas as conexões HTTP na porta de destino 8080 para utilizar o modo de transporte IPsec. Como o HTTP é um protocolo de texto não criptografado, ter a política de segurança ativada garante que os dados são encriptados quando é transferido através do peering privado do ExpressRoute. Política de segurança de IP para o Active Directory é mais complexa de configurar do que a Firewall do Windows com segurança avançada, mas permite uma personalização mais da ligação IPsec.

## <a name="assigngpo"></a>8. Atribua o GPO de IPsec para a UO

1. Ver a política. A política de grupo de segurança é definida, mas ainda não foi atribuída.

   [![49]][49]
2. Para atribuir a política de grupo de segurança para a UO **IPSecOU**, clique com o botão direito a política de segurança e escolheu **atribuir**.
   Cada tht computador pertence à UO terão a política de grupo de segurança atribuída.

   [![50]][50]

## <a name="checktraffic"></a>Verificar a encriptação de tráfego

Verificar a encriptação GPO aplicado na UO de, instale o IIS em todas as VMs do Azure e no host1. Todos os IIS é personalizado para responder a pedidos de HTTP na porta 8080.
Para verificar a encriptação, pode instalar uma rede sniffer (farejador) (como Wireshark) em todos os computadores na UO.
Um script do powershell funciona como um cliente HTTP para gerar pedidos HTTP na porta 8080:

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
A seguinte captura de rede mostra que os resultados para o host1 no local com apresentam ESP de filtro para corresponder apenas o tráfego criptografado:

[![51]][51]

Se executar o powershell script no-premisies (cliente HTTP), a captura de rede na VM do Azure mostra um rastreio semelhante.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações acerca do ExpressRoute, veja as [FAQs do ExpressRoute](expressroute-faqs.md).

<!--Image References-->

[1]: ./media/expressroute-howto-ipsec-transport-private-windows/network-diagram.png "modo de transporte do IPsec de diagrama de rede através do ExpressRoute"
[4]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-interesting-traffic.png "IPsec interessantes de tráfego"
[5]: ./media/expressroute-howto-ipsec-transport-private-windows/windows-ipsec.png "política IPsec do Windows"
[9]: ./media/expressroute-howto-ipsec-transport-private-windows/ou.png "unidade organizacional na política de grupo"
[10]: ./media/expressroute-howto-ipsec-transport-private-windows/create-gpo-ou.png "criar um GPO associado a UO"
[11]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-name.png "atribuir um nome para o GPO associado a UO"
[12]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-gpo.png "editar o GPO"
[15]: ./media/expressroute-howto-ipsec-transport-private-windows/manage-ip-filter-list-filter-actions.png "gerir listas de filtros IP e filtrar ações"
[16]: ./media/expressroute-howto-ipsec-transport-private-windows/add-filter-action.png "Adicionar filtro de ação"
[17]: ./media/expressroute-howto-ipsec-transport-private-windows/action-wizard.png "Assistente de ação"
[18]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-name.png "nome da ação de filtro"
[19]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action.png "filtrar ação"
[20]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-no-ipsec.png "especificar o comportamento é é estabelecida uma ligação não segura"
[21]: ./media/expressroute-howto-ipsec-transport-private-windows/security-method.png "mecanismo de segurança"
[22]: ./media/expressroute-howto-ipsec-transport-private-windows/custom-security-method.png "método de segurança personalizado"
[23]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-actions-list.png "lista de filtros de ação"
[24]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-ip-filter.png "adicionar uma nova lista de filtro IP"
[25]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-http-traffic.png "tráfego HTTP adicionar ao filtro de IP"
[26]: ./media/expressroute-howto-ipsec-transport-private-windows/match-both-direction.png "pacotes de correspondência em ambas as direções"
[27]: ./media/expressroute-howto-ipsec-transport-private-windows/source-address.png "seleção da sub-rede de origem"
[28]: ./media/expressroute-howto-ipsec-transport-private-windows/source-network.png "da origem de rede"
[29]: ./media/expressroute-howto-ipsec-transport-private-windows/destination-network.png "rede de destino"
[30]: ./media/expressroute-howto-ipsec-transport-private-windows/protocol.png "protocolo"
[31]: ./media/expressroute-howto-ipsec-transport-private-windows/source-port-and-destination-port.png "da origem de porta e a porta de destino"
[32]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list.png "a lista de filtros"
[33]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-for-http.png "lista de filtros IP com o tráfego HTTP"
[34]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-add-second-entry.png "adicionando um segundo filtro IP"
[35]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-second-entry.png "entrada de lista-segundo filtro IP"
[36]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list-2entries.png "entrada de lista-segundo filtro IP"
[37]: ./media/expressroute-howto-ipsec-transport-private-windows/create-ip-security-policy.png "criar a política de segurança IP"
[38]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-policy-name.png "nome da política IPsec"
[39]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-wizard.png "Assistente de política de IPsec"
[40]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-security-policy.png "Editar da política IPsec"
[41]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-rule.png "Adicionar nova regra de segurança para a política de IPsec"
[42]: ./media/expressroute-howto-ipsec-transport-private-windows/create-security-rule.png "criar uma nova regra de segurança"
[43]: ./media/expressroute-howto-ipsec-transport-private-windows/transport-mode.png "modo de transporte"
[44]: ./media/expressroute-howto-ipsec-transport-private-windows/network-type.png "tipo de rede"
[45]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-list.png "seleção de lista de filtros de IP existente"
[46]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-action.png "seleção da ação de filtro existente"
[47]: ./media/expressroute-howto-ipsec-transport-private-windows/authentication-method.png "seleção do método de autenticação"
[48]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-completed.png "final do processo de criação de política de segurança"
[49]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-not-assigned.png "política de IPsec associado ao GPO, mas não atribuído"
[50]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-assigned.png "atribuída para o GPO de política de IPsec"
[51]: ./media/expressroute-howto-ipsec-transport-private-windows/encrypted-traffic.png "capturar de IPsec encriptados tráfego"
