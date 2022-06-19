
## [Leetcode 206 : Reverse a linked list](https://leetcode.com/problems/reverse-linked-list/)

```cpp

//my written code

class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        ListNode* iter = head;
        ListNode* prev = nullptr;
        ListNode* next = nullptr;
        while(iter) {
            next = iter->next;
            iter->next = prev;
            prev = iter;
            iter = next;
        }
        return prev;
    }
};

```

----

## Reverse Linked List II

Reverse a linked list from position m to n. Do it in-place and in one-pass.

For example:
Given 1->2->3->4->5->NULL, m = 2 and n = 4,
return 1->4->3->2->5->NULL.

Note: Given m, n satisfy the following condition: 1 ≤ m ≤ n ≤ length of list.

```cpp

ListNode* reverseBetween(ListNode* head, int m, int n) {
    ListNode newHead(0);
    newHead.next = head;
    ListNode *pre = &newHead, *cur = head, *next = NULL;
    int i = 1;
    while(i < n) {
        if(i++ < m) { pre = cur; cur = cur->next; }
        else { 
            next = cur->next; 
            cur->next = cur->next->next; 
            next->next = pre->next; 
            pre->next = next; 
        }
    }
    return newHead.next;
}

```

----

## [Leetcode 876: Middle of the Linked List](https://leetcode.com/problems/middle-of-the-linked-list/)

Slow pointer and fast pointer approach.

```cpp
class Solution {
public:
    ListNode* middleNode(ListNode* head) {
        ListNode* slow = head;
        ListNode* fast = head;
        while(fast && fast->next) {
            slow = slow->next;
            fast = fast->next->next;
        }
        return slow;
    }
};
```


----


## [Leetcode 21: merge two sorted lists](https://leetcode.com/problems/merge-two-sorted-lists/)

Similar to merge operation of merge sort.

```cpp

class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        // iterative 
        ListNode* res = new ListNode(); 
        ListNode *temp = res; 
        
        while(l1 != NULL && l2!=NULL) {
            if(l1->val < l2->val) {
                res->next = l1; 
                res = res -> next; 
                l1 = l1->next; 
            }
            else {
                res->next = l2; 
                res = res->next; 
                l2 = l2->next; 
            }
        }
        while(l1 != NULL) {
            res->next = l1; 
            res = res -> next; 
            l1 = l1->next; 
        }
        while(l2 != NULL) {
            res->next = l2; 
            res = res -> next; 
            l2 = l2->next; 
        }
        
        return temp->next;
    }
};
```

----

## [Leetcode 19: Remove Nth Node From End of List](https://leetcode.com/problems/remove-nth-node-from-end-of-list/)

<br />Step1 : Move fast pointer by N nodes
<br />Step2 : Slow points to start of list, Fast points to Nth node, Keep iterating list until fast points to null.
<br />Step3 : Remove node pointed by slow.

Make sure to add null checks everywhere.

```cpp

class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        ListNode *slow, *fast=head, *dummy,*ret;
        slow = new ListNode(0);
        slow->next = head;
        fast = slow;
        dummy = slow;
        for(int i=0; i<n; i++) {
            fast = fast ? fast->next : nullptr;
        }
        while(fast && fast->next!= nullptr) {
            slow = slow->next;
            fast = fast->next;
        }
        slow->next = slow->next ? slow->next->next : nullptr;
        ret = dummy->next;
        delete dummy;
        return ret;
    }
};

```

----

## [Leetcode 2: Add Two Numbers](https://leetcode.com/problems/add-two-numbers/)

```
ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
    int c = 0;
    ListNode newHead(0);
    ListNode *t = &newHead;
    while(c || l1 || l2) {
        c += (l1? l1->val : 0) + (l2? l2->val : 0);
        t->next = new ListNode(c%10);
        t = t->next;
        c /= 10;
        if(l1) l1 = l1->next;
        if(l2) l2 = l2->next;
    }
    return newHead.next;
}

```

## Remove Duplicates from Sorted List

Given 1->1->2, return 1->2.

```cpp

ListNode* deleteDuplicates(ListNode* head) {
    if(!head) return head;
    ListNode *t = head, *p = head->next;
    int pre = head->val;
    while(p) {
        if(pre != p->val) {
            t->next = p;
            pre = p->val;
            t = t->next;
        }
        p = p->next;
    }
    t->next = NULL;
    return head;
}

```

## Remove Duplicates from Sorted List II

Given 1->1->1->2->3, return 2->3. 

Use dummy node to simplify code.

```cpp

ListNode* deleteDuplicates(ListNode* head) {
	ListNode* dummy = new ListNode(0);
	dummy->next = head;
	ListNode* cur = dummy;
	int duplicate;
	while (cur->next && cur->next->next) {
		if (cur->next->val == cur->next->next->val) {
			duplicate = cur->next->val;
			while (cur->next && cur->next->val == duplicate) 
				cur->next = cur->next->next;
		}
		else cur = cur->next;
	}
	return dummy->next;
}

//Recursive

ListNode* deleteDuplicates(ListNode* head) {
    if (!head) return 0;
    if (!head->next) return head;
    int val = head->val;
    ListNode* p = head->next;
    if (p->val != val) { head->next = deleteDuplicates(p); return head;} 
    else { 
        while (p && p->val == val) p = p->next; 
        return deleteDuplicates(p); 
    }
}

```

----


## Check if Palindrome Linked List

<br />Find middle of linked list.
<br />Set next of middle node to null.
<br />Reverse second half.
<br />Compare.

```cpp

bool isPalindrome(ListNode* head) {
    if(!head || !head->next) return true;
    ListNode *slow = head, *fast = head->next;
    while(fast && fast->next) {//split into two halves while the first half can be one-node longer;
        slow = slow->next;
        fast = fast->next->next;
    }
    fast = slow->next;
    slow->next = NULL;
    ListNode newHead(0); //reverse the second half;
    ListNode *next = NULL, *p = fast;
    while(p) {
        next = p->next;
        p->next = newHead.next;
        newHead.next = p;
        p = next;
    }
    fast = newHead.next; //compare the two lists;
    while(fast) {
        if(fast->val != head->val) return false;
        fast = fast->next;
        head = head->next;
    }
    return fast == NULL;
}

```

## 160. Intersection of Two Linked Lists

```cpp

/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        if(headA == NULL || headB == NULL) return NULL;
    
        ListNode *a = headA;
        ListNode *b = headB;

        //if a & b have different len, then we will stop the loop after second iteration
        while( a != b){
            //for the end of first iteration, we just reset the pointer to the head of another linkedlist
            a = a == NULL? headB : a->next;
            b = b == NULL? headA : b->next;    
        }

        return a;

    }
};



```


## Detect cycle in linked list

``` cpp

/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    bool hasCycle(ListNode *head) {
        if(head == NULL || head -> next == NULL)    
            return false;
 
        ListNode *fast = head;
        ListNode *slow = head;
        
        while(fast -> next && fast -> next -> next){
            fast = fast -> next -> next;
            slow = slow -> next;
            if(fast == slow)
                return true;
        }
 
        return false;
    }
};


```


## Check if Palindromic linked list

Find middle of linked list.
Reverse right half.
Compare from start and mid. if equal then palindromic.


```cpp

class Solution {
    public boolean isPalindrome(ListNode head) {
                if(head==null||head.next==null)
            return true;
        ListNode slow=head;
        ListNode fast=head;
        while(fast.next!=null&&fast.next.next!=null){
            slow=slow.next;
            fast=fast.next.next;
        }
        slow.next=reverseList(slow.next);
        slow=slow.next;
        while(slow!=null){
            if(head.val!=slow.val)
                return false;
            head=head.next;
            slow=slow.next;
        }
        return true;
    }
    ListNode reverseList(ListNode head) {
        ListNode pre=null;
        ListNode next=null;
        while(head!=null){
            next=head.next;
            head.next=pre;
            pre=head;
            head=next;
        }
        return pre;
    }
}


```

## Reverse nodes in groups of K

Focus on reversing 1 link.
Do above step K times.

```cpp

/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* reverseKGroup(ListNode* head, int k) {
        if(head == NULL || k == 1) return head;
        ListNode *dummy = new ListNode(0);
        dummy->next = head;
        ListNode *cur = dummy , *nex = dummy, *pre = dummy;
        int count = 0;
        while(cur->next != NULL){
            cur = cur->next;
            count++;
        }
        while(count >= k){
            cur = pre->next;
            nex = cur->next;
            for(int i = 1; i < k; i++){
                cur->next = nex->next;
                nex->next = pre->next;
                pre->next = nex;
                nex = cur->next;
            }
            pre = cur;
            count-=k;
        }
        return dummy->next;
    }
};

```


## Clone a Linked List with next and random pointer

Naive:
Create map of <Node* existingNode, Node* newNode>
Replace pointers in new nodes.

Optimal approach:
Insert new nodes next to old nodes. Now you know where is new node for a given old node without map.


```cpp

/*
// Definition for a Node.
class Node {
public:
    int val;
    Node* next;
    Node* random;
    
    Node(int _val) {
        val = _val;
        next = NULL;
        random = NULL;
    }
};
*/

class Solution {
public:
    Node* copyRandomList(Node* head) {
        Node *iter = head; 
          Node *front = head;

          // First round: make copy of each node,
          // and link them together side-by-side in a single list.
          while (iter != NULL) {
            front = iter->next;

            Node *copy = new Node(iter->val);
            iter->next = copy;
            copy->next = front;

            iter = front;
          }

          // Second round: assign random pointers for the copy nodes.
          iter = head;
          while (iter != NULL) {
            if (iter->random != NULL) {
              iter->next->random = iter->random->next;
            }
            iter = iter->next->next;
          }

          // Third round: restore the original list, and extract the copy list.
          iter = head;
          Node *pseudoHead = new Node(0);
          Node *copy = pseudoHead;

          while (iter != NULL) {
            front = iter->next->next;

            // extract the copy
            copy->next = iter->next;

            // restore the original list
            iter->next = front;
              
            copy = copy -> next; 
            iter = front;
          }

          return pseudoHead->next;
    }
};


```

## [Leetcode 61: Rotate List](https://leetcode.com/problems/rotate-list/description/)


Given the head of a linked list, rotate the list to the right by k places.

Solution:
Get size of linked list and do % k operation. This will save rotation by size of list.
To rotate by K, keep fast pointer ahead by size-K and point it to null.
Point dummy->next to next of fast.
Point end of list to start of list.
