---
title: Conceitos - Identidade e acesso
description: Conheça os conceitos de identidade e acesso da Azure VMware Solution
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 1d49ce27ee74621874281a555651c09a73048928
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109820"
---
# <a name="azure-vmware-solution-identity-concepts"></a>Conceitos de identidade de solução Azure VMware

As nuvens privadas Azure VMware Solution são a provisionadas com um servidor vCenter e um gestor NSX-T. Utiliza o vCenter para gerir as cargas de trabalho da máquina virtual (VM) e o NSX-T Manager para gerir e estender a nuvem privada. O acesso e a gestão de identidade utilizam a função CloudAdmin para vCenter e direitos de administrador restritos para O Gestor NSX-T. 

Para mais informações, consulte [o artigo conceitos de atualizações de nuvem privada.][concepts-upgrades]

## <a name="vcenter-access-and-identity"></a>vCenter acesso e identidade

Na Azure VMware Solution, o vCenter tem um utilizador local incorporado chamado cloudadmin e atribuído ao papel cloudAdmin. O utilizador local de cloudadmin é utilizado para configurar utilizadores no Ative Directory (AD). Em geral, o papel CloudAdmin cria e gere cargas de trabalho na sua nuvem privada. Mas na Azure VMware Solution, o papel CloudAdmin tem privilégios vCenter que diferem de outras soluções de nuvem VMware.     

- Numa implementação vCenter e ESXi no local, o administrador tem acesso à \@ conta vCenter administrador vsphere.local. Também podem ter mais utilizadores e grupos de AD atribuídos. 

- Numa implementação da Azure VMware Solution, o administrador não tem acesso à conta de utilizador do administrador. Podem, no entanto, atribuir utilizadores e grupos de AD ao papel cloudAdmin no vCenter.  

O utilizador privado da nuvem não tem acesso e não consegue configurar componentes de gestão específicos suportados e geridos pela Microsoft. Por exemplo, clusters, anfitriões, datastores e comutadores virtuais distribuídos.

> [!IMPORTANT]
> A Azure VMware Solution oferece funções personalizadas no vCenter, mas atualmente não as oferece no portal Azure VMware Solution. Para obter mais informações, consulte as [funções personalizadas Criar na secção vCenter](#create-custom-roles-on-vcenter) mais tarde neste artigo. 

### <a name="view-the-vcenter-privileges"></a>Ver os privilégios vCenter

Pode ver os privilégios concedidos ao papel cloudAdmin da Solução Azure VMware no seu VMware Solution privado VMware Solution vCenter.

1. Inicie sessão no SDDC vSphere Client e vá para a   >  **Administração** do Menu.
1. Sob **controlo de acesso**, selecione **Roles**.
1. Na lista de funções, selecione **CloudAdmin** e, em seguida, selecione **Privileges**. 

   :::image type="content" source="media/role-based-access-control-cloudadmin-privileges.png" alt-text="Como ver os privilégios de papel cloudAdmin no vSphere Client":::

O papel cloudAdmin na Azure VMware Solution tem os seguintes privilégios no vCenter. Para mais detalhes, consulte a documentação do [produto VMware](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html).

| Privilege | Description |
| --------- | ----------- |
| **Alarmes** | Alarme de reconhecimento<br />Criar alarme<br />Desativar a ação de alarme<br />Modificar o alarme<br />Remover o alarme<br />Definir estado de alarme |
| **Biblioteca de Conteúdos** | Adicionar item da biblioteca<br />Criar uma subscrição para uma biblioteca publicada<br />Criar biblioteca local<br />Criar biblioteca subscrita<br />Apagar item da biblioteca<br />Apagar biblioteca local<br />Excluir biblioteca subscrita<br />Excluir subscrição de uma biblioteca publicada<br />Transferir ficheiros<br />Despejar artigos de biblioteca<br />Despejar biblioteca subscrita<br />Armazenamento de importação<br />Informações de subscrição da sonda<br />Publicar um item de biblioteca para os seus assinantes<br />Publicar uma biblioteca para os seus assinantes<br />Ler armazenamento<br />Artigo da biblioteca sincronizada<br />Biblioteca sincronizada<br />Introspeção tipo<br />Atualizar definições de configuração<br />Atualizar ficheiros<br />Atualizar biblioteca<br />Atualizar item da biblioteca<br />Atualizar biblioteca local<br />Atualização da biblioteca subscrita<br />Atualizar subscrição de uma biblioteca publicada<br />Ver definições de configuração |
| **Operações criptográficas** | Acesso direto |
| **Arquivo de dados** | Alocar espaço<br />Procurar loja de dados<br />Configure datastore<br />Operações de ficheiro de baixo nível<br />Remover ficheiros<br />Atualizar metadados de máquinas virtuais |
| **Pasta** | Criar pasta<br />Eliminar pasta<br />Mover pasta<br />Pasta de renome |
| **Global** | Cancelar tarefa<br />Etiqueta global<br />Saúde<br />Evento de registo<br />Gerir atributos personalizados<br />Gestores de serviços<br />Definir atributo personalizado<br />Etiqueta de sistema |
| **Anfitrião** | replicação vSphere<br />&#160;&#160;&#160;&#160;Gerir a replicação |
| **Rede** | Atribuir rede |
| **Permissões** | Modificar permissões<br />Alterar papel |
| **Perfil** | Vista de armazenamento orientada para o perfil |
| **Recurso** | Aplicar recomendação<br />Atribuir vApp ao pool de recursos<br />Atribuir máquina virtual ao pool de recursos<br />Criar piscina de recursos<br />Migrar alimentado fora da máquina virtual<br />Migrar alimentado em máquina virtual<br />Modificar o conjunto de recursos<br />Mover piscina de recursos<br />Consulta vMotion<br />Remover piscina de recursos<br />Rebatize piscina de recursos |
| **Tarefa agendada** | Criar tarefa<br />Modificar tarefa<br />Remover tarefa<br />Executar tarefa |
| **Sessões** | Mensagem<br />Validar sessão |
| **Vista de armazenamento** | Vista |
| **vApp** | Adicionar máquina virtual<br />Atribuir piscina de recursos<br />Atribuir vApp<br />Clone<br />Criar<br />Eliminar<br />Exportar<br />Importar<br />Mover<br />Desligar<br />Ligado<br />Mudar o Nome<br />Suspender<br />Anular o Registo<br />Ver ambiente OVF<br />configuração de aplicação vApp<br />configuração de instância vApp<br />vApp gerido Por configuração<br />configuração de recursos vApp |
| **Máquina virtual** | Alterar configuração<br />&#160;&#160;&#160;&#160;Adquirir arrendamento em disco<br />&#160;&#160;&#160;&#160;Adicionar disco existente<br />&#160;&#160;&#160;&#160;Adicionar novo disco<br />&#160;&#160;&#160;&#160;Adicionar ou remover dispositivo<br />&#160;&#160;&#160;&#160;Configuração avançada<br />contagem de CPU de mudança de &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;Mudar memória<br />Definições de alteração &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;Alterar a colocação do ficheiro de swap<br />&#160;&#160;&#160;&#160;Alterar recurso<br />&#160;&#160;&#160;&#160;Configurar dispositivo USB anfitrião<br />&#160;&#160;&#160;&#160;Configurar dispositivos brutos<br />&#160;&#160;&#160;&#160;Configure gerido Por<br />&#160;&#160;&#160;&#160;Definições de conexão de exibição<br />&#160;&#160;&#160;&#160;Estender o disco virtual<br />&#160;&#160;&#160;&#160;Modificar as definições do dispositivo<br />&#160;&#160;&#160;&#160;Consulta compatibilidade da tolerância à falha<br />&#160;&#160;&#160;&#160;Consulta ficheiros não-damedados<br />&#160;&#160;&#160;&#160;Recarregue dos caminhos<br />&#160;&#160;&#160;&#160;Remover o disco<br />&#160;&#160;&#160;&#160;Renome<br />&#160;&#160;&#160;&#160;Redefinir informações dos hóspedes<br />anotação do conjunto &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;Toggle para alterar o rastreio do disco<br />&#160;&#160;&#160;&#160;Toggle, pai do garfo<br />&#160;&#160;&#160;&#160;Atualizar compatibilidade da máquina virtual<br />Editar inventário<br />&#160;&#160;&#160;&#160;Criar a partir de existir<br />&#160;&#160;&#160;&#160;Criar novo<br />movimento &#160;&#160;&#160;&#160;<br />Registo &#160;&#160;&#160;&#160;<br />remover &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;Não Registro<br />Operações de hóspedes<br />&#160;&#160;&#160;&#160;Operação Convidado modificação de pseudónimo<br />&#160;&#160;&#160;&#160;Consulta de pseudónimos de operação de hóspedes<br />&#160;&#160;&#160;&#160;Modificações de operação do hóspede<br />Execução do programa de operação &#160;&#160;&#160;&#160;Convidado<br />consultas de operação &#160;&#160;&#160;&#160;convidado<br />Interação<br />pergunta &#160;&#160;&#160;&#160;Resposta<br />&#160;&#160;&#160;&#160;operação de back up na máquina virtual<br />&#160;&#160;&#160;&#160;Configurar os meios de comunicação do CD<br />&#160;&#160;&#160;&#160;Configurar os meios de comunicação<br />&#160;&#160;&#160;&#160;Ligar dispositivos<br />interação &#160;&#160;&#160;&#160;consola<br />&#160;&#160;&#160;&#160;Criar screenshot<br />&#160;&#160;&#160;&#160;Defragment todos os discos<br />&#160;&#160;&#160;&#160;Arrastar e largar<br />&#160;&#160;&#160;&#160;Gestão do sistema operativo Guest pela VIX API<br />&#160;&#160;&#160;&#160;injetar códigos de verificação USB HID<br />&#160;&#160;&#160;&#160;Instalar ferramentas VMware<br />pausa &#160;&#160;&#160;&#160;ou unpause<br />&#160;&#160;&#160;&#160;Limpeza ou redução<br />&#160;&#160;&#160;&#160;Desligar<br />&#160;&#160;&#160;&#160;Power on<br />Sessão de &#160;&#160;&#160;&#160;Record na máquina virtual<br />Sessão de repetição de &#160;&#160;&#160;&#160;na máquina virtual<br />suspensão &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;suspender a tolerância à falha<br />Teste de &#160;&#160;&#160;&#160;falha<br />teste de &#160;&#160;&#160;&#160;reinicia VM secundário<br />&#160;&#160;&#160;&#160;Desligue a tolerância à falha<br />&#160;&#160;&#160;&#160;Ligue a tolerância à falha<br />Aprovisionamento<br />&#160;&#160;&#160;&#160;Permitir o acesso ao disco<br />&#160;&#160;&#160;&#160;Permitir o acesso a ficheiros<br />&#160;&#160;&#160;&#160;Permitir o acesso ao disco apenas de leitura<br />&#160;&#160;&#160;&#160;Permitir o download de máquinas virtuais<br />modelo de clone de &#160;&#160;&#160;&#160;<br />máquina virtual clone de &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;Criar modelo a partir de máquina virtual<br />&#160;&#160;&#160;&#160;Personalize o hóspede<br />&#160;&#160;&#160;&#160;modelo de implementação<br />&#160;&#160;&#160;&#160;Mark como modelo<br />&#160;&#160;&#160;&#160;Modificar especificações de personalização<br />&#160;&#160;&#160;&#160;Promover discos<br />&#160;&#160;&#160;&#160;Ler especificações de personalização<br />Configuração do serviço<br />&#160;&#160;&#160;&#160;Permitir notificações<br />&#160;&#160;&#160;&#160;Permitir sondagens de notificações globais de eventos<br />&#160;&#160;&#160;&#160;Gerir a configuração do serviço<br />&#160;&#160;&#160;&#160;Modificar a configuração do serviço<br />&#160;&#160;&#160;&#160;configurações de serviço de consulta<br />&#160;&#160;&#160;&#160;Ler a configuração do serviço<br />Gestão instantânea<br />&#160;&#160;&#160;&#160;Criar instantâneo<br />&#160;&#160;&#160;&#160;Remover instantâneo<br />&#160;&#160;&#160;&#160;instantâneo do nome<br />&#160;&#160;&#160;&#160;Revert snapshot<br />replicação vSphere<br />&#160;&#160;&#160;&#160;Configurar a replicação<br />&#160;&#160;&#160;&#160;Gerir a replicação<br />replicação do monitor de &#160;&#160;&#160;&#160; |
| **vServiço** | Criar dependência<br />Destruir a dependência<br />Reconfigurar a configuração da dependência<br />Atualizar a dependência |
| **marcação vSphere** | Atribuir e não assinar vSphere tag<br />Criar tag vSphere<br />Criar categoria de tags vSphere<br />Eliminar tag vSphere<br />Eliminar categoria de tags vSphere<br />Editar tag vSphere<br />Editar categoria tag vSphere<br />Modificar o campo UsedBy para categoria<br />Modificar o campo UsedBy para etiqueta |

### <a name="create-custom-roles-on-vcenter"></a>Criar funções personalizadas no vCenter

A Azure VMware Solution suporta o uso de funções personalizadas com privilégios iguais ou inferiores ao papel cloudAdmin. 

O papel CloudAdmin pode criar, modificar ou eliminar funções personalizadas que tenham privilégios inferiores ou iguais ao seu papel atual. Poderá ser capaz de criar funções que tenham privilégios maiores do que o CloudAdmin, mas não poderá atribuir o papel a nenhum utilizadores ou grupos ou apagar o papel.

Para evitar a criação de funções que não podem ser atribuídas ou eliminadas, a Azure VMware Solution recomenda a clonagem do papel cloudAdmin como base para a criação de novas funções personalizadas.

#### <a name="create-a-custom-role"></a>Criar uma função personalizada
1. Inscreva-se no vCenter com cloudadmin \@ vsphere.local ou um utilizador com o papel CloudAdmin.
2. Navegue para a secção de configuração **de funções** e selecione   >    >    >  **Funções de** Controlo de Acesso à Administração do Menu .
3. Selecione a função **CloudAdmin** e selecione o ícone **de ação de função Clone.**

   > [!NOTE] 
   > Não clone o papel **de Administrador.** Esta função não pode ser usada e o papel personalizado criado não pode ser eliminado por cloudadmin \@ vsphere.local.

4. Forneça o nome que quiser para o papel clonado.
5. Adicione ou remova privilégios para o papel e selecione **OK**. O papel clonado deve agora ser visível na lista **de papéis.**


#### <a name="use-a-custom-role"></a>Use um papel personalizado

1. Navegue para o objeto que requer a permissão adicional. Por exemplo, para aplicar a permissão a uma pasta, navegue para **o nome** de pasta  >  **de VMs e modelos**  >  **do** Menu
1. Clique com o botão direito no objeto e **selecione Adicionar Permissão**.
1. Na janela **'Adicionar Permissão',** selecione a Fonte de Identidade no drop-down do **Utilizador** onde o grupo ou o utilizador podem ser encontrados.
1. Procure o utilizador ou grupo depois de selecionar a Fonte de Identidade na secção **Utilizador.** 
1. Selecione a função que será aplicada ao utilizador ou grupo.
1. Verifique a **Propagação para as crianças,** se necessário, e selecione **OK**.
   A permissão adicional aparece na secção **Permissões** do objeto.

## <a name="nsx-t-manager-access-and-identity"></a>Acesso e identidade do Gerente NSX-T

>[!NOTE]
>O NSX-T 2.5 está atualmente suportado.

Utilize a conta *de administrador* para aceder ao NSX-T Manager. Tem plenos privilégios e permite criar e gerir Gateways Tier-1 (T1), segmentos (comutadores lógicos) e todos os serviços. Os privilégios dão-lhe acesso ao Gateway NSX-T Tier-0 (T0). Uma alteração no Gateway T0 pode resultar num desempenho de rede degradado ou sem acesso privado à nuvem. Abra um pedido de suporte no portal Azure para solicitar quaisquer alterações ao seu Gateway NSX-T T0.

 
## <a name="next-steps"></a>Passos seguintes

Agora que cobriu os conceitos de acesso e identidade da Azure VMware Solution, talvez queira saber:

- [Conceitos de upgrade de nuvem privada](concepts-upgrades.md)
- [Como ativar o recurso Azure VMware Solution](enable-azure-vmware-solution.md)
- [Detalhes de cada privilégio](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html)
- [Como a Azure VMware Solution monitoriza e repara nuvens privadas](concepts-monitor-repair-private-cloud.md)
- [Como ativar o recurso Azure VMware Solution](enable-azure-vmware-solution.md)


<!-- LINKS - external-->
[VMware product documentation]: https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md
