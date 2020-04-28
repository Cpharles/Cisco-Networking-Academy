# Operação de Roteamento entre Vlnas

**Existem três opções de roteamento entre VLANs:**
1. Roteamento entre VLANs herdados:
	:   Esta é uma solução herdada. Não escala bem.  "Este método de roteamento entre VLAN não é mais implementado em redes comutadas"
2. Router-on-a-Stick:
	:   Esta é uma solução aceitável para uma rede pequena e média.
3. Switch da camada 3 usando interfaces virtuais comutadas (SVIs):
	:   Esta é a solução mais escalável para organizações de médio a grande porte.

**2. ROUTER-ON-STICK INTER-VLAN ROUTING:**
  - **Etapa 1.** Crie e nomeie as VLANs.
  - **Etapa 2.** Crie a interface de gerenciamento (SVI), mas não é um passo obrigatório, lembrando que é para atribuir o acesso ao uma porta de configuração.
  - **Passo 3.** Configure portas de acesso.
  - **Etapa 4.** Configure portas de entroncamento.
 
Cenario:
![Router-on-a-Stick](https://github.com/Cpharles/Cisco-Networking-Academy/blob/master/CCNA2v7%20-%20Switching-Routing-Wireless%20Essentials/Packet%20Tracer/Cap%204/screenshots/2-Router-on-a-Stick%20Scenario.png)

**Exemplo:**

`Etapa 1`
```
S1(config)# vlan 10
S1(config-vlan)# name LAN10
S1(config-vlan)# exit
S1(config)# vlan 20
S1(config-vlan)# name LAN20
S1(config-vlan)# exit
S1(config)# vlan 99
S1(config-vlan)# name Management
S1(config-vlan)# exit
S1(config)#
```
`Etapa 2`
```
S1(config)# interface vlan 99
S1(config-if)# ip add 192.168.99.2 255.255.255.0
S1(config-if)# no shut
S1(config-if)# exit
S1(config)# ip default-gateway 192.168.99.1
S1(config)#
```
`Etapa 3`
```
S1(config)# interface fa0/6
S1(config-if)# switchport mode access
S1(config-if)# switchport access vlan 10
S1(config-if)# no shut
S1(config-if)# exit
S1(config)#
```
`Etapa 4`
```
S1(config)# interface fa0/1
S1(config-if)# switchport mode trunk
S1(config-if)# no shut
S1(config-if)# exit
S1(config)# interface fa0/5
S1(config-if)# switchport mode trunk
S1(config-if)# no shut
S1(config-if)# end
```

**2.1. CONFIGURAÇÃO DA SUBINTERFACE NO ROTEADOR (R1):**
O método ROUTER-ON-STICK exige que você crie uma subinterface para cada VLAN a ser roteada.
- Entrar na interface
- Criar as subinterface
- Associar o DOT1Q a VLAN
- Atribuir o endereço IP
- Entrar na interface
- Ativar a interface
```
R1(config)# interface G0/0/1.10
R1(config-subif)# description Default Gateway for VLAN 10
R1(config-subif)# encapsulation dot1Q 10
R1(config-subif)# ip add 192.168.10.1 255.255.255.0
R1(config-subif)# exit
R1(config)#
R1(config)# interface G0/0/1.20
R1(config-subif)# description Default Gateway for VLAN 20
R1(config-subif)# encapsulation dot1Q 20
R1(config-subif)# ip add 192.168.20.1 255.255.255.0
R1(config-subif)# exit
R1(config)#
R1(config)# interface G0/0/1.99
R1(config-subif)# description Default Gateway for VLAN 99
R1(config-subif)# encapsulation dot1Q 99 native
R1(config-subif)# ip add 192.168.99.1 255.255.255.0
R1(config-subif)# exit
R1(config)#
R1(config)# interface G0/0/1
R1(config-if)# description Trunk link to S1
R1(config-if)# no shut
R1(config-if)# end
```

**2.2. VERIFICAÇÃO DO ROTEAMENTO ROUTER-ON-STICK INTER-VLAN:**
```
R1# show ip route | begin Gateway
(Verifique se as subinterfaces estão aparecendo na tabela de roteamento do R1)

R1# show ip interface brief | include up
(confirma que as subinterfaces têm o endereço IPv4 correto configurado e que estão operacionais.)

R1# show interfaces g0/0/1.10
(Verificando as subinterfaces

S1# show interfaces trunk
(Verificar os links de trunk ativos em um switch da Camada 2)
```
