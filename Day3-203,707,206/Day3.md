# Leetcode - 203 Remove Linked List Element

## Take-aways

1. **How to declare a dummyHead? Esp how to understand the argument in `dummy_head = ListNode(next = head)`?**
   - The `ListNode(next=head)` creates a new node with a default value (0) that points to your original head.
   - This dummy head acts as a sentinel node that helps handle edge cases, especially when you need to remove the first element.
   - It's essentially creating a new node and immediately setting its `next` pointer to your original list.

2. **How to move along a linked list in Python? (in C, I can increment the pointer to the head to move along)**
   - In Python, you move through a linked list by updating a reference variable to point to the next node:
   - `current = current.next` advances your position by one node.
   - Unlike C where you directly manipulate memory addresses, Python handles this at a higher level through references.

3. **Why not use `dummyHead` to move forward? Instead, we declare `current = dummyHead` before the traverse?**
   - We keep `dummyHead` as a fixed reference to the start of our modified list.
   - We use `current` as our traversal pointer that moves through the list.
   - This allows us to return `dummyHead.next` at the end, which is the new head of the modified list.
   - If we moved `dummyHead` itself, we'd lose our reference to the start of the list.

4. **We always check if `next` is the target. The check does not happen to the value of the current node?**
   - Correct. We check `current.next.val` because we need to know about the node *before* the one we want to remove.
   - To remove a node in a linked list, you need to modify the `next` pointer of its predecessor.
   - This pattern lets us safely "bridge over" the node to be removed without losing our position.

5. **For the Python Class `ListNode`, are `val` and `next` referring to the value of the current element and the value of the next, not like in C, which point to addresses in memory?**
   - `val` is indeed the data value stored in the current node.
   - `next` is a reference to the next node object, not its value.
   - While Python doesn't expose raw memory addresses like C, `next` is conceptually similar to a pointer - it "points to" the next node object.

6. **Is the `val` in Class `ListNode` not the same as the `val` in the function `removeElements`?**
   - They're different. `ListNode.val` is an instance variable of each node object.
   - The `val` parameter in `removeElements(head, val)` is the target value you want to remove from the list.
   - We're comparing each node's `val` attribute against this target `val` parameter.

7. **AGAIN IMPORTANT: we are manipulating the `next` node instead of the current**
   - Absolutely correct. The key insight is that we're changing the `next` pointer of the current node, not the current node itself.
   - When we find a node to remove, we set `current.next = current.next.next` to bypass it.
   - This is why we start checks from the dummy head - it lets us handle removing the first real node if needed.

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def removeElements(self, head: Optional[ListNode], val: int) -> Optional[ListNode]:
        dummyHead = ListNode(next=head)
        current = dummyHead
        while current and current.next:
            if current.next.val == val:
                current.next = current.next.next if current.next.next else None
            # current = current.next
            else:
                current = current.next  # Only advance if we didn't remove a node
        return dummyHead.next
```

# Leetcode - 707 Design Linked List

## Key Questions and Answers

1. **Why do I need to declare a ListNode structure outside the given boilerplate? Why can't I declare such structure in `__init__` of `MyLinkedList` class?**
   - You need to declare it outside because it's a separate data structure that defines what a node contains.
   - Placing it in `__init__` would redefine it every time you create a new linked list instance, which is inefficient and can cause scoping issues.

2. **Why doesn't `dummyHead` here need to receive the argument `next=head` like in other linked list problems?**
   - The dummy head doesn't need `next=head` because we're not connecting it to an existing head - we're creating an empty list with it as a sentinel node.
   - The dummy head serves as a placeholder before the actual list begins, making operations more uniform.

3. **Why do we use `current = dummyHead.next` instead of `current = dummyHead` in some methods? How does this affect when to use `range(index)` vs `range(index + 1)`?**
   - When using `current = dummyHead.next`, you start at the first actual node (index 0).
   - When using `current = dummyHead`, you start at the sentinel node (before index 0).
   - For operations that need the target node (get), use `dummyHead.next` and `range(index)`
   - For operations that need the node before the target (insert/delete), use `dummyHead` and `range(index)`

4. **For method `addAtHead`, what is the current head in this Class? Is it `dummyHead` declared in `__init__`?**
   - `dummyHead.next` is the actual head of the list, not `dummyHead` itself.
   - `dummyHead` is just a sentinel node that points to the real head.
   - When adding at head, you're inserting between the dummy head and the current first node.

5. **Why don't I need to consider `index = 0` as a special case for the `deleteAtIndex` method?**
   - You don't need special handling for `index = 0` because the dummy head approach makes deletion uniform.
   - You navigate to the node before the target using the dummy head, then adjust the pointers.
   - This works for all valid indices including 0 (first element).

## Common Implementation Pitfalls

1. **Boundary Checks**: Always use `index >= self.size` not `index > self.size` to prevent accessing out-of-bounds elements.

2. **Null Reference Errors**: Starting traversal from `dummy_head.next` can cause errors with empty lists - use `dummy_head` for operations like `addAtTail`.

3. **Instance Variables**: Remember to use `self.` for instance variables in `__init__` to make them available throughout the class.

4. **Consistent Naming**: Be consistent with naming conventions (`dummy_head` vs `dummyHead`) to avoid attribute errors.

5. **Node Creation**: Always create a new ListNode object when adding elements, not directly assigning values to `next` attributes.

```python

class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

class MyLinkedList:

    def __init__(self):
        self.dummy_head = ListNode()
        self.size = 0

    def get(self, index: int) -> int:
        if index < 0 or index >= self.size:
            return -1
        
        current = self.dummy_head.next
        for _ in range(index):
            current = current.next
        
        return current.val 

    def addAtHead(self, val: int) -> None:
        self.dummy_head.next = ListNode(val, self.dummy_head.next)
        self.size += 1

    def addAtTail(self, val: int) -> None:
        current = self.dummy_head
        # for _ in range(self.size): # easier: while current.next:
        while current.next:
            current = current.next
        
        # current.next = val
        current.next = ListNode(val, current.next)
        self.size += 1

    def addAtIndex(self, index: int, val: int) -> None:
        if index < 0 or index > self.size:
            return
        
        current = self.dummy_head
        for _ in range(index): 
            current = current.next
        temp = current.next
        current.next = ListNode(val) # again, remember we operate on .next not the current node ! 
        current.next.next = temp
        self.size += 1

    def deleteAtIndex(self, index: int) -> None:
        if index < 0 or index >= self.size:
            return
        
        current = self.dummy_head
        for _ in range(index):
            current = current.next
        
        # if current.next:
        current.next = current.next.next
        # else:
        #     current.next = null
        self.size -= 1

# Your MyLinkedList object will be instantiated and called as such:
# obj = MyLinkedList()
# param_1 = obj.get(index)
# obj.addAtHead(val)
# obj.addAtTail(val)
# obj.addAtIndex(index,val)
# obj.deleteAtIndex(index)
```

# Leetcode - 206 Reverse Linked List

## Understanding the Problem
**Q: What are we trying to do in the "Reverse Linked List" problem?**  
A: We need to reverse the direction of a singly linked list so that each node points to the previous node instead of the next node.

**Q: What should be the result of reversing 1→2→3→NULL?**  
A: The result should be NULL←1←2←3, with 3 becoming the new head of the list.

## Common Confusions

**Q: Why do we need three pointers to reverse a linked list?**  
A: We need to track:
1. `prev` - Where we came from (to point our current node back to)
2. `current` - The node we're currently working on
3. `next_temp` - Where we're going next (so we don't lose our place after changing pointers)

**Q: What happens if we set `current.next = current` as I tried in my code?**  
A: This creates a self-referential loop where a node points to itself, rather than reversing the direction. For example, if we're at node 1 and we do `current.next = current`, node 1 will point back to itself instead of pointing to the previous node.

**Q: What's the issue with my original approach?**
```python
dummyHead = LinkedList(next = currentHead) # pointer to the original head
current = dummyHead # use this one to move along the linked list
while current.next:
  temp = current.next
  current.next = current  # This creates a loop
  current = temp
```
A: This code creates a self-referential loop. When `current` is dummyHead and we do `current.next = current`, we're making dummyHead point to itself instead of reversing the direction.

**Q: If I create a dummyHead with `dummyHead = LinkedList(next = head)`, is dummyHead null?**  
A: No, dummyHead is not null. It's an actual node object whose "next" field points to the original head of the list.

**Q: Why do we return `prev` instead of `current` at the end of the algorithm?**  
A: After the loop completes, `current` will be `None` because we've gone past the end of the list. The `prev` pointer will be pointing to the last node of the original list, which has become the new head of our reversed list.

**Q: In the line `current.next = prev`, what exactly are we doing?**  
A: We're changing where the current node points to. Instead of pointing to the next node in the original list, we're making it point to the previous node, effectively reversing the direction of the link.
Again, my confution was to understand `current.next` as the actual object of the next node. Instead, it's the pointer from the current node !

## My Final Correct Solution
```python
def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
    prev = None
    current = head
    
    while current:
        temp = current.next
        current.next = prev
        prev = current
        current = temp
    
    return prev
```

## Optimized Solution (same as above)
```python
def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
    prev = None
    current = head
    
    while current:
        next_temp = current.next  # Save next before changing
        current.next = prev       # Reverse the pointer
        prev = current            # Move prev forward
        current = next_temp       # Move current forward
    
    return prev  # Return the new head
```

## Visualization of Algorithm Execution
For input: 1→2→3→NULL

**Initialization:**
- `prev = None`
- `current = 1`

**Iteration 1:**
- Save `next_temp = 2`
- Change `1.next = None`
- Move `prev = 1`
- Move `current = 2`
- List becomes: `None←1 2→3→NULL`

**Iteration 2:**
- Save `next_temp = 3`
- Change `2.next = 1`
- Move `prev = 2`
- Move `current = 3`
- List becomes: `None←1←2 3→NULL`

**Iteration 3:**
- Save `next_temp = NULL`
- Change `3.next = 2`
- Move `prev = 3`
- Move `current = NULL`
- List becomes: `None←1←2←3`

**Loop terminates, return `prev` (3)**