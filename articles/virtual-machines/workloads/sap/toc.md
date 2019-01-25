# Descrição geral
## [Introdução](get-started.md)
## [Certificações](sap-certifications.md)
# SAP HANA no Azure (Instâncias Grandes)
## Descrição geral
### [O que é o SAP HANA no Azure (Instâncias Grandes)?](hana-overview-architecture.md)
### [Conhecer os termos](hana-know-terms.md)
### [Certificação](hana-certification.md)
### [SKUs Disponíveis para HLI](hana-available-skus.md)
### [Dimensionamento](hana-sizing.md)
### [Requisitos de integração](hana-onboarding-requirements.md)
### [Nós de extensão e colocação de dados em camadas do SAP HANA](hana-data-tiering-extension-nodes.md)
### [Modelo e responsabilidades de operações](hana-operations-model.md)
## Arquitetura
### [Arquitetura geral](hana-architecture.md)
### [Arquitetura de rede](hana-network-architecture.md)
### [Arquitetura de armazenamento](hana-storage-architecture.md)
### [Cenários HLI suportados ](hana-supported-scenario.md)
## Infraestrutura e conectividade
### [Implementação de HLI](hana-overview-infrastructure-connectivity.md)
### [Ligar VMs do Azure para Instâncias Grandes do HANA](hana-connect-azure-vm-large-instances.md)
### [Ligar uma VNet para ExpressRoute de Instância Grande do HANA](hana-connect-vnet-express-route.md)
### [Requisitos de rede adicionais](hana-additional-network-requirements.md)
## Instalar o SAP HANA
### [Validar a configuração](hana-installation.md)
### [Instalação do HANA de exemplo](hana-example-installation.md)
## Elevada disponibilidade e recuperação após desastre
### [Opções e considerações](hana-overview-high-availability-disaster-recovery.md)
### [Backup e restauro](hana-backup-restore.md)
### [Princípios e preparação](hana-concept-preparation.md)
### [Procedimento de ativação pós-falha de recuperação após desastre](hana-failover-procedure.md)
## Resolver problemas e monitorizar
### [Monitorização HLI](troubleshooting-monitoring.md)
### [Monitorizar e resolver problemas do lado do HANA](hana-monitor-troubleshoot.md)
## Procedimento
### [Configuração HA com STONITH](ha-setup-with-stonith.md)
### [Cópia de segurança de SO para SKUs de Tipo II](os-backup-type-ii-skus.md)
### [Atualização do SO para Instâncias Grandes do HANA](os-upgrade-hana-large-instance.md)
### [Configurar o servidor SMT para SUSE Linux](hana-setup-smt.md)
# SAP HANA nas Máquinas Virtuais do Azure
## [Instalação do SAP HANA de instância única](hana-get-started.md)
## [Guia de implementação de S/4 HANA ou de BW/4 HANA SAP CAL](cal-s4h.md)
## [Configurações e operações de infraestrutura do SAP HANA no Azure](hana-vm-operations.md)
## Disponibilidade do SAP HANA nas Máquinas Virtuais do Azure
### [SAP HANA na descrição geral da Disponibilidade do Azure](sap-hana-availability-overview.md)
### [SAP HANA na descrição geral da Disponibilidade do Azure numa região do Azure](sap-hana-availability-one-region.md)
### [SAP HANA na descrição geral da Disponibilidade do Azure pelas regiões do Azure](sap-hana-availability-across-regions.md)
### [Configurar a Replicação do Sistema SAP HANA no SLES](sap-hana-high-availability.md)
### [Configurar a Replicação do Sistema SAP HANA no RHEL](sap-hana-high-availability-rhel.md)
### [Resolver problemas de dimensionamento do SAP HANA e do Pacemaker no SLES](hana-vm-troubleshoot-scale-out-ha-on-sles.md)
## [Descrição geral da cópia de segurança do SAP HANA](sap-hana-backup-guide.md)
## [Cópia de segurança ao nível do ficheiro do SAP HANA](sap-hana-backup-file-level.md)
## [Cópias de segurança de instantâneos de armazenamento do SAP HANA](sap-hana-backup-storage-snapshots.md)
# SAP NetWeaver e Business One em Máquinas Virtuais do Azure
## [Lista de verificação de planeamento e implementação de carga de trabalho do SAP](sap-deployment-checklist.md)
## [Planear e implementar o SAP NetWeaver no Azure](planning-guide.md)
## [Guia de implementação do SAP NetWeaver](deployment-guide.md)
## Guias de implementação do DBMS para a carga de trabalho SAP
### [Implementação geral do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP](dbms_guide_general.md)
### [Implementação em SQL Server do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP](dbms_guide_sqlserver.md)
### [Implementação em Oracle do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP](dbms_guide_oracle.md)
### [Implementação em IBM DB2 do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP](dbms_guide_ibm.md)
### [Implementação em SAP ASE do DBMS para Máquinas Virtuais do Azure para a carga de trabalho SAP](dbms_guide_sapase.md)
### [Implementação do SAP MaxDB, liveCache e Servidor de Conteúdos no Azure](dbms_guide_maxdb.md)
### Disponibilidade do SAP HANA nas Máquinas Virtuais do Azure
#### [SAP HANA na descrição geral da Disponibilidade do Azure](sap-hana-availability-overview.md)
#### [SAP HANA na descrição geral da Disponibilidade do Azure numa região do Azure](sap-hana-availability-one-region.md)
#### [SAP HANA na descrição geral da Disponibilidade do Azure pelas regiões do Azure](sap-hana-availability-across-regions.md)
## [SAP Business One em Máquinas Virtuais do Azure](business-one-azure.md)
## [Guia de implementação de SAP IDES em SAP CAL no Windows/SQL Server](cal-ides-erp6-erp7-sp3-sql.md)
## [Conector de SAP LaMa para o Azure](lama-installation.md)
## Elevada Disponibilidade (HA) no Windows e Linux
### [Descrição geral](sap-high-availability-guide-start.md)
### Arquitetura de Elevada Disponibilidade
#### [Arquitetura e Cenários de HA](sap-high-availability-architecture-scenarios.md)
#### [Arquitetura e Cenários de Elevada Disponibilidade](sap-higher-availability-architecture-scenarios.md)
#### [HA no Windows com Disco Partilhado para a Instância (A)SCS](sap-high-availability-guide-wsfc-shared-disk.md)
#### [HA no Windows com Partilha de Ficheiros SOFS para a Instância (A)SCS](sap-high-availability-guide-wsfc-file-share.md)
#### [HA no SUSE Linux para a Instância (A)SCS](high-availability-guide-suse.md)
#### [HA no Red Hat Enterprise Linux para Instância de (A)SCS](high-availability-guide-rhel.md)
### Preparação da Infraestrutura do Azure
#### [Windows com Disco Partilhado para a Instância (A)SCS](sap-high-availability-infrastructure-wsfc-shared-disk.md)
#### [Windows com Partilha de Ficheiros SOFS para a Instância (A)SCS](sap-high-availability-infrastructure-wsfc-file-share.md)
#### [Disponibilidade elevada para NFS nas VMs do Azure no SLES](high-availability-guide-suse-nfs.md)
#### [GlusterFS nas VMs do Azure no Red Hat Enterprise Linux para o SAP NetWeaver](high-availability-guide-rhel-glusterfs.md)
#### [Pacemaker no SLES](high-availability-guide-suse-pacemaker.md)
#### [Pacemaker no RHEL](high-availability-guide-rhel-pacemaker.md)
### Instalação de SAP
#### [Windows com Disco Partilhado para a Instância (A)SCS](sap-high-availability-installation-wsfc-shared-disk.md)
#### [Windows com Partilha de Ficheiros SOFS para a Instância (A)SCS](sap-high-availability-installation-wsfc-file-share.md)
#### [SUSE Linux com NFS para a Instância (A)SCS](high-availability-guide-suse.md)
#### [Alta disponibilidade para o SAP NetWeaver em Red Hat Enterprise Linux](high-availability-guide-rhel.md)
### Múltiplos SID SAP
#### [Windows com Disco Partilhado para a Instância (A)SCS](sap-ascs-ha-multi-sid-wsfc-shared-disk.md)
#### [Windows com Partilha de Ficheiros SOFS para a Instância (A)SCS](sap-ascs-ha-multi-sid-wsfc-file-share.md)
##  [Azure Site Recovery para Recuperação após Desastre do SAP](../../../site-recovery/site-recovery-workload.md#protect-sap)
# Integração de Identidade do AAD SAP e Início de Sessão único
## [Integração com o SAP Cloud](../../../active-directory/saas-apps/sap-customer-cloud-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [Integração do AAD com a Autenticação de Identidade da Plataforma Cloud do SAP](../../../active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [Configurar o Início de Sessão Único com a Plataforma Cloud do SAP](../../../active-directory/saas-apps/sap-hana-cloud-platform-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [Integração do AAD com o SAP NetWeaver](../../../active-directory/saas-apps/sap-netweaver-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [Integração do AAD com o SAP Business ByDesign](../../../active-directory/saas-apps/sapbusinessbydesign-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
## [Integração do AAD com o SAP HANA DBMS](../../../active-directory/saas-apps/saphana-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
##[Início de Sessão Único SAML do SAP Fiori Launchpad com o Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad)
# Integração de Serviços do Azure ao SAP
## [Utilizar o SAP HANA no Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-hana)
## [DirectQuery e SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
## [Utilizar o conector SAP BW no Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector)
## [O Azure Data Factory oferece integração de dados do SAP HANA e Business Warehouse](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)
# Recursos
## [Mapa do Azure](https://azure.microsoft.com/roadmap/)

