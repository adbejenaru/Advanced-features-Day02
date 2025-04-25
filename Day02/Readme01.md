![image](https://github.com/user-attachments/assets/f7ca2b0f-1bc8-415e-8ea4-bd223a614ec8)![image](https://github.com/user-attachments/assets/c15ade50-7a4b-4819-86fc-93a8df7babeb)
Mai jos găsești o explicație amplă și corectă a colecțiilor din Java, care îmbină definițiile generale, structura ierarhică şi detaliile fiecărei categorii de colecţii:

---

# Colecțiile în Java – Java Collections Framework (JCF)

**Colecțiile în Java** sunt structuri de date care grupează și gestionează obiectele eficient, oferind operații precum adăugarea, eliminarea, căutarea sau sortarea elementelor. Ele fac parte din pachetul `java.util` și se regăsesc în **Java Collections Framework (JCF)**, un set standardizat de interfețe, clase și algoritmi.

---

## 1. Rădăcina – `Iterable<E>` și `Collection<E>`

1. **`Iterable<E>`**  
   - Defineste un singur metodă: `Iterator<E> iterator()`  
   - Orice colecție Java poate fi parcursă cu `for (E e : colecţie)`

2. **`Collection<E>`**  
   ```java
   public interface Collection<E> extends Iterable<E> { … }
   ```  
   - *extinde* `Iterable<E>` (interfețe se extind, clasele le implementează)  
   - Operații generale:  
     - `boolean add(E e)`  
     - `boolean remove(Object o)`  
     - `int size()`  
     - `void clear()`  
     - `boolean contains(Object o)`  
   - Interfețe derivate: `List`, `Set`, `Queue`, `Deque`  

---

## 2. Ierarhia principală  

```text
               Iterable<E>
                   ↓
             Collection<E>                      Map<K,V>
              ↙     ↓     ↘                         ↓
           List    Set    Queue              ┌──── SortedMap<K,V>
           ↓       ↓      ↘ ↖                 │
    (ArrayList,  (HashSet,  Deque)            └→ TreeMap<K,V>
     LinkedList)  LinkedHashSet,
                 TreeSet)
```

*Notă:* `Map<K,V>` nu extinde `Collection<E>`, e o ramură separată sub `Iterable<Map.Entry<K,V>>`.

---

## 3. `List<E>`

- **Descriere**: colecție **ordonată**, indexată, permite **duplicate** și valori `null`.  
- **Operații cheie**:  
  ```java
  add(E e), add(int idx, E e),
  get(int idx), set(int idx, E e),
  remove(int idx), remove(Object o),
  indexOf(Object o), size(), clear()
  ```  
- **Implementări uzuale**:  
  | Clasă                  | Structură internă      | Acces      | Inserții/Ștergeri           | Securitate thread-safe |
  |------------------------|------------------------|------------|-----------------------------|------------------------|
  | `ArrayList<E>`         | array dinamic          | O(1)       | O(n) (redimensionare)       | nu                     |
  | `LinkedList<E>`        | listă dublu-înlănțuită | O(n)       | O(1) (când ai referință la nod) | nu                  |
  | `Vector<E>`            | array sincronizat      | O(1)       | O(n)                        | da                     |
  | `CopyOnWriteArrayList<E>` | array copiat la scriere | O(1)    | O(n) (copiere întreg)       | da                     |

- **Când alegi**:  
  - Acces rapid prin index → `ArrayList`  
  - Inserții/ștergeri frecvente → `LinkedList`  
  - Medii multithread de citire intensivă → `CopyOnWriteArrayList`

---

## 4. `Set<E>`

- **Descriere**: colecție **fără duplicate**. Nu există ordine garantată, cu excepția implementărilor specializate.  
- **Operații cheie**:  
  ```java
  add(E e), remove(Object o),
  contains(Object o), size(), clear()
  ```  
- **Implementări uzuale**:  
  | Clasă / Interfață         | Descriere                                        | Complexitate         |
  |---------------------------|--------------------------------------------------|----------------------|
  | `HashSet<E>`              | tabelă hash, ordine nedefinită                  | O(1) amortizat       |
  | `LinkedHashSet<E>`        | `HashSet` + listă menținere inserție             | O(1) amortizat       |
  | `SortedSet<E>` (interfață)→ `TreeSet<E>` | arbore roșu-negru, elemente **sortate** | O(log n)             |
  | `EnumSet<E extends Enum>` | bitset optimizat pentru enum-uri                 | foarte rapid, compact |
  | `ConcurrentSkipListSet<E>`| skip-list, varianta thread-safe                   | O(log n)             |

- **Când alegi**:  
  - Ordine de inserție importantă → `LinkedHashSet`  
  - Elemente sortate automat → `TreeSet`  
  - Enum-uri → `EnumSet`  
  - Concurență fără blocări → `ConcurrentSkipListSet`

---

## 5. `Queue<E>`

- **Descriere**: coadă **FIFO** (First In, First Out).  
- **Operații cheie**:  
  ```java
  offer(E e), add(E e),
  poll(), remove(),
  peek(), element()
  ```  
- **Implementări uzuale**:  
  | Clasă                  | Comportament                         |
  |------------------------|--------------------------------------|
  | `LinkedList<E>`        | listă dublu-înlănțuită, suportă `Queue` |
  | `PriorityQueue<E>`     | cap = element cu prioritatea cea mai mare |
  | `ArrayBlockingQueue<E>`| coadă blocantă cu capacitate fixă    |
  | `LinkedBlockingQueue<E>`| coadă blocantă legată, capacitate opțională |
  | `ConcurrentLinkedQueue<E>` | coadă neblocantă                 |

- **Când alegi**:  
  - Prioritizare task-uri → `PriorityQueue`  
  - Producer–consumer → `ArrayBlockingQueue` / `LinkedBlockingQueue`  
  - Concurență fără blocări → `ConcurrentLinkedQueue`

---

## 6. `Deque<E>`

- **Descriere**: coadă dublă, permite adăugare/extragere la **ambele capete**.  
- **Operații cheie**:  
  ```java
  addFirst(E), addLast(E),
  removeFirst(), removeLast(),
  peekFirst(), peekLast()
  ```  
- **Implementări uzuale**:  
  | Clasă          | Structură internă    |
  |----------------|----------------------|
  | `ArrayDeque<E>`| array circular       |
  | `LinkedList<E>`| listă dublu-înlănțuită |

- **Când alegi**: performanțe consistente pentru operații la capete → `ArrayDeque`

---

## 7. `Map<K,V>`

- **Descriere**: colecție de perechi **cheie → valoare**, cheile sunt unice.  
- **Operații cheie**:  
  ```java
  put(K key, V value),
  get(Object key),
  remove(Object key),
  containsKey(Object key),
  keySet(), values(), entrySet()
  ```  
- **Implementări uzuale**:  
  | Clasă / Interfață         | Descriere                                    | Complexitate      |
  |---------------------------|----------------------------------------------|-------------------|
  | `HashMap<K,V>`            | tabelă hash, `null` ca și cheie/valoare      | O(1) amortizat    |
  | `LinkedHashMap<K,V>`      | `HashMap` + ordine inserție                  | O(1) amortizat    |
  | `SortedMap<K,V>` → `TreeMap<K,V>` | arbore roșu-negru, chei sortate      | O(log n)          |
  | `Hashtable<K,V>`          | sincronizat, veche                            | O(1) amortizat    |
  | `EnumMap<K extends Enum, V>` | optimizat pt. `enum`                    | foarte rapid      |
  | `ConcurrentHashMap<K,V>`  | varianta concurentă fără blocări              | O(1) amortizat    |

- **Când alegi**:  
  - Menținere ordine inserție → `LinkedHashMap`  
  - Chei sortate → `TreeMap`  
  - Concurență intensă → `ConcurrentHashMap`

---

## 8. Utilitare și Stream API

- **`java.util.Collections`**:  
  - `sort(List)`, `binarySearch(List, key)`, `shuffle(List)`  
  - `synchronizedList(List)`, `unmodifiableSet(Set)`  
- **`java.util.Arrays`**:  
  - `asList(array)`, `sort(array)`, `binarySearch(array, key)`  
- **Stream API (Java 8+)**:  
  - Operații funcționale: `stream().filter(...)`, `map(...)`, `collect(...)` – se pot aplica pe orice colecție sau array.

---

## Sfaturi de Bună Practică

1. **Programează după interfață**, nu după implementare.  
2. **Alege implementarea optimă** în funcție de:  
   - Mod de acces (indexat vs. parcurgere)  
   - Frecvență inserții/ștergeri vs. citiri  
   - Nevoi de sortare automată  
   - Concurență și thread-safety  
3. **Evită `null`** acolo unde nu e permis (ex. `TreeSet`, `TreeMap`).  
4. **Folosește utilitare** din `Collections` și `Arrays` pentru operații comune și pentru a obține view-uri thread-safe sau imutabile.

---

Această prezentare combină definiția generală, ierarhia corectă și detaliile despre fiecare tip de colecție din JCF, astfel încât să ai un **document complet, coerent și ușor de predat**.
