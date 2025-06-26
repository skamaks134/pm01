# pm01
Задание 2. Объясните, что такое LACP и для чего он используется. Опишите процесс формирования LACP агрегированных каналов. Какие преимущества использования LACP по сравнению с другими методами агрегации каналов? Какие состояния могут быть у LACP портов и что они означают? Вам необходимо настроить LACP в соответствии с предоставленным топологическим планом. Также нужно провести диагностику и устранение неполадок(если есть) в конфигурации LACP. У вас есть два коммутатора Cisco Catalyst 2960 - SW1 и SW2. Коммутаторы соединены двумя линками: Линк 1: Порт FastEthernet0/1 на SW1 подключен к порту FastEthernet0/1 на SW2; Линк 2: Порт FastEthernet0/2 на SW1 подключен к порту FastEthernet0/2 на SW2.
Ответ на Задание 2: LACP (Link Aggregation Control Protocol)
1. Что такое LACP и для чего он используется?
LACP (IEEE 802.3ad) — это протокол динамической агрегации каналов, который объединяет несколько физических линков в один логический для:

Увеличения пропускной способности (суммирование скорости портов).

Обеспечения отказоустойчивости (если один линк отвалится, трафик пойдет через другие).

Балансировки нагрузки (трафик распределяется между линками).

2. Процесс формирования LACP-агрегированных каналов
Настройка LACP на коммутаторах:

Создается Port-Channel (логический интерфейс).

Порты добавляются в группу агрегации.

Указывается режим LACP (active или passive).

Обмен LACP-пакетами (PDU):

Устройства обмениваются служебными кадрами для согласования параметров.

Формирование агрегированного канала:

Если настройки совпадают, линки объединяются в один логический канал.

3. Преимущества LACP перед другими методами агрегации
Метод	Преимущества LACP
Статическая агрегация (без LACP)	LACP автоматически проверяет работоспособность линков.
PAgP (Cisco-proprietary)	LACP — стандартный протокол (межвендорная совместимость).
Load Balancing без агрегации	LACP обеспечивает отказоустойчивость и балансировку.
4. Состояния LACP-портов
Состояние	Описание
Active	Порт активно пытается установить LACP-агрегацию.
Passive	Порт отвечает на LACP-запросы, но не инициирует их.
Bundle	Порт успешно агрегирован в Port-Channel.
Individual	Порт не смог объединиться (несовместимые настройки).
Настройка LACP между SW1 и SW2
Топология
Линк 1: SW1: Fa0/1 ↔ SW2: Fa0/1

Линк 2: SW1: Fa0/2 ↔ SW2: Fa0/2

Конфигурация на SW1
bash
enable
configure terminal
interface range FastEthernet0/1-2
  channel-group 1 mode active  # Включаем LACP в активном режиме
  exit
interface Port-channel 1
  switchport mode trunk  # Если используется VLAN
  exit
end
Конфигурация на SW2
bash
enable
configure terminal
interface range FastEthernet0/1-2
  channel-group 1 mode active
  exit
interface Port-channel 1
  switchport mode trunk
  exit
end
Проверка работы LACP
bash
show etherchannel summary  # Проверка состояния агрегации
show lacp neighbor  # Проверка соседних LACP-устройств
Диагностика неполадок
Порты не агрегируются:

Проверить, что на обоих коммутаторах одинаковые настройки (active/active или active/passive).

Убедиться, что кабели исправны и порты включены (no shutdown).

Нет трафика через Port-Channel:

Проверить, что VLAN разрешены на trunk-портах (switchport trunk allowed vlan ...).

Задание 3. Объясните, что такое OSPF и как он работает. Опишите процесс выбора DR (Designated Router) и BDR (Backup Designated Router). Какие типы LSAs (Link State Advertisements) существуют и какую информацию они несут? Как OSPF определяет стоимость пути (path cost) и какие параметры влияют на этот расчет? Вам необходимо настроить OSPF в соответствии с предоставленным топологическим планом, также нужно провести диагностику и устранение неполадок(если есть) в сети OSPF.  У вас есть три коммутатора Cisco 2960 - SW1, SW2 и SW3. Сеть состоит из следующих сегментов: Сегмент 1: SW1 подключен к SW2 через сеть 10.0.12.0/24; Сегмент 2: SW2 подключен к SW3 через сеть 10.0.23.0/24; Сегмент 3: SW1 подключен к SW3 через сеть 10.0.13.0/24. Все три коммутатора также подключены к своим локальным сетям: SW1: 192.168.1.0/24; SW2: 192.168.2.0/24; SW3: 192.168.3.0/24.

Ответ на Задание 3: OSPF (Open Shortest Path First)
1. Что такое OSPF и как он работает?
OSPF — это протокол динамической маршрутизации, основанный на алгоритме Dijkstra.

Использует Link-State Advertisements (LSA) для обмена информацией о топологии.

Работает в multi-area иерархии (Area 0 — backbone).

Метрика (cost) вычисляется по формуле:

c
o
s
t
=
10
8
b
a
n
d
w
i
d
t
h
(
b
p
s
)
cost= 
bandwidth(bps)
10 
8
 
​
 
(например, для 100 Mbps: cost = 1).

2. Выбор DR (Designated Router) и BDR (Backup DR)
DR отвечает за рассылку LSA в multi-access-сетях (Ethernet).

BDR становится DR в случае его отказа.

Выбор происходит по приоритету (priority):

Если приоритет 0 — устройство не участвует в выборе.

Если приоритеты равны — выбирается устройство с наибольшим Router ID.

3. Типы LSA
Тип LSA	Описание
Type 1 (Router LSA)	Описывает линки маршрутизатора внутри area.
Type 2 (Network LSA)	Генерируется DR для multi-access-сетей.
Type 3 (Summary LSA)	Анонсирует маршруты между area (от ABR).
Type 5 (External LSA)	Анонсирует внешние маршруты (из EIGRP/static).
4. Расчет стоимости пути (cost)
Зависит от пропускной способности интерфейса (например, 100 Mbps → cost=1, 10 Mbps → cost=10).

Можно изменить вручную:

bash
interface FastEthernet0/1
  ip ospf cost 50
Настройка OSPF между SW1, SW2, SW3
Топология
Сегмент 1: SW1 ↔ SW2 (10.0.12.0/24)

Сегмент 2: SW2 ↔ SW3 (10.0.23.0/24)

Сегмент 3: SW1 ↔ SW3 (10.0.13.0/24)

Локальные сети:

SW1: 192.168.1.0/24

SW2: 192.168.2.0/24

SW3: 192.168.3.0/24

Конфигурация на SW1
bash
enable
configure terminal
router ospf 1
  network 10.0.12.0 0.0.0.255 area 0
  network 10.0.13.0 0.0.0.255 area 0
  network 192.168.1.0 0.0.0.255 area 0
  exit
end
Конфигурация на SW2
bash
router ospf 1
  network 10.0.12.0 0.0.0.255 area 0
  network 10.0.23.0 0.0.0.255 area 0
  network 192.168.2.0 0.0.0.255 area 0
  exit
Конфигурация на SW3
bash
router ospf 1
  network 10.0.13.0 0.0.0.255 area 0
  network 10.0.23.0 0.0.0.255 area 0
  network 192.168.3.0 0.0.0.255 area 0
  exit
Проверка работы OSPF
bash
show ip ospf neighbor  # Проверка соседей
show ip route ospf     # Таблица маршрутизации OSPF
show ip ospf database  # База данных LSA
Диагностика неполадок
Нет соседей (neighbors):

Проверить, что интерфейсы в одном Area.

Убедиться, что нет ACL/Firewall, блокирующих OSPF (пакеты 224.0.0.5 и 224.0.0.6).

Маршруты не появляются:

Проверить network-команды в OSPF.

Убедиться, что интерфейсы не в passive-interface.

### **Полная настройка LACP и OSPF через FRRouting (FRR)**

---

## **1. Настройка LACP (Link Aggregation) между SW1 и SW2**

### **Топология**
- **SW1** (Ports: `eth1`, `eth2`) ↔ **SW2** (Ports: `eth1`, `eth2`)  
- Агрегированный канал (`bond0`) с LACP в режиме `802.3ad`.

---

### **Настройка на SW1 (Debian/Ubuntu + FRR)**
#### **1. Установка пакетов**
```bash
sudo apt update
sudo apt install ifenslave frr
```

#### **2. Настройка агрегации (bonding)**
```bash
sudo nano /etc/network/interfaces
```
Добавляем:
```bash
# Bonding interface (LACP)
auto bond0
iface bond0 inet manual
    bond-mode 802.3ad
    bond-miimon 100
    bond-lacp-rate 1
    bond-slaves eth1 eth2
    bond-xmit-hash-policy layer3+4

# Физические интерфейсы
auto eth1
iface eth1 inet manual
    bond-master bond0

auto eth2
iface eth2 inet manual
    bond-master bond0
```
Применяем:
```bash
sudo systemctl restart networking
```

#### **3. Проверка агрегации**
```bash
cat /proc/net/bonding/bond0
```
Вывод должен содержать:
```bash
LACP rate: fast
Aggregator selection policy (ad_select): stable
Active Aggregator Info:
    LACP state: Negotiation
    Number of ports: 2
```

---

### **Настройка на SW2 (аналогично SW1)**
```bash
sudo nano /etc/network/interfaces
```
```bash
auto bond0
iface bond0 inet manual
    bond-mode 802.3ad
    bond-miimon 100
    bond-lacp-rate 1
    bond-slaves eth1 eth2
    bond-xmit-hash-policy layer3+4

auto eth1
iface eth1 inet manual
    bond-master bond0

auto eth2
iface eth2 inet manual
    bond-master bond0
```
```bash
sudo systemctl restart networking
```

---

## **2. Настройка OSPF между SW1, SW2, SW3**

### **Топология**
- **SW1** (`192.168.1.1`) ↔ **SW2** (`192.168.2.1`) ↔ **SW3** (`192.168.3.1`)  
- **Сети:**
  - `10.0.12.0/24` (SW1 ↔ SW2)  
  - `10.0.23.0/24` (SW2 ↔ SW3)  
  - `10.0.13.0/24` (SW1 ↔ SW3)  

---

### **Настройка FRR (OSPF) на SW1**
#### **1. Включение OSPF в FRR**
```bash
sudo vtysh
```
```bash
configure terminal
router ospf
    network 10.0.12.0/24 area 0
    network 10.0.13.0/24 area 0
    network 192.168.1.0/24 area 0
    exit
exit
write
```

#### **2. Назначение IP-адресов**
```bash
sudo ip addr add 10.0.12.1/24 dev bond0
sudo ip addr add 10.0.13.1/24 dev eth3  # Если есть третий интерфейс
sudo ip addr add 192.168.1.1/24 dev eth0
```

---

### **Настройка FRR (OSPF) на SW2**
```bash
sudo vtysh
```
```bash
configure terminal
router ospf
    network 10.0.12.0/24 area 0
    network 10.0.23.0/24 area 0
    network 192.168.2.0/24 area 0
    exit
exit
write
```
```bash
sudo ip addr add 10.0.12.2/24 dev bond0
sudo ip addr add 10.0.23.1/24 dev eth3
sudo ip addr add 192.168.2.1/24 dev eth0
```

---

### **Настройка FRR (OSPF) на SW3**
```bash
sudo vtysh
```
```bash
configure terminal
router ospf
    network 10.0.13.0/24 area 0
    network 10.0.23.0/24 area 0
    network 192.168.3.0/24 area 0
    exit
exit
write
```
```bash
sudo ip addr add 10.0.13.2/24 dev bond0
sudo ip addr add 10.0.23.2/24 dev eth3
sudo ip addr add 192.168.3.1/24 dev eth0
```

---

## **3. Проверка работы OSPF и LACP**

### **Проверка OSPF-соседей**
```bash
sudo vtysh
show ip ospf neighbor
```
Вывод должен быть примерно таким:
```bash
Neighbor ID     Pri   State      Dead Time   Address    Interface
192.168.2.1     1    Full/DR    00:00:35    10.0.12.2  bond0
192.168.3.1     1    Full/BDR   00:00:37    10.0.13.2  eth3
```

### **Проверка таблицы маршрутизации**
```bash
show ip route ospf
```
Пример вывода:
```bash
O 10.0.23.0/24 [110/10] via 10.0.12.2, bond0
O 192.168.2.0/24 [110/20] via 10.0.12.2, bond0
```

### **Проверка LACP**
```bash
cat /proc/net/bonding/bond0
```
```bash
LACP rate: fast
Slave Interface: eth1
    MII Status: up
    Aggregator ID: 1
Slave Interface: eth2
    MII Status: up
    Aggregator ID: 1
```

---

## **4. Диагностика и устранение неполадок**

### **Если OSPF не работает**
1. **Проверить физическое соединение** (`ping 10.0.12.2`).
2. **Убедиться, что OSPF включен**:
   ```bash
   sudo vtysh
   show running-config
   ```
3. **Проверить `network` в OSPF** (должны быть правильные подсети).

### **Если LACP не работает**
1. **Проверить `bonding`-интерфейс**:
   ```bash
   ip link show bond0
   ```
2. **Убедиться, что оба порта `up`**:
   ```bash
   ethtool eth1 | grep "Link detected"
   ```
3. **Проверить настройки `bond-mode`** (должно быть `802.3ad`).

---

### **Итог**
- **LACP** настроен через `bonding` в режиме `802.3ad`.  
- **OSPF** работает через FRR, маршруты автоматически обновляются.  
- Проверка через `vtysh` и `ip route`.  
- Если что-то не работает — смотреть логи (`journalctl -u frr`).
