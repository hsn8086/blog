``` python
class LazySegmentTree:
    def __init__(
        self,
        data,
        merge_func,
        update_func,
        lazy_merge_func,
        default_val=0,
        lazy_default_val=0,
    ):
        """
        Initialize a Lazy Segment Tree.

        Parameters:
        data (list): The initial data array
        merge_func (function): Function to merge two values (lambda a, b: ...)
        update_func (function): Function to apply a tag to a value (lambda val, tag, l, r: ...)
        lazy_merge_func (function): Function to merge two tags (lambda old_tag, new_tag: ...)
        default_val: Default value for filling empty tree slots
        lazy_default_val: Default value for lazy tags
        """
        self.n = len(data)
        self.merge = merge_func
        self.update = update_func
        self.lazy_merge = lazy_merge_func
        self.lazy_default_val = lazy_default_val

        # Calculate tree size (next power of 2 >= n)
        self.size = 1
        while self.size < self.n:
            self.size <<= 1

        # Initialize tree and lazy arrays
        self.tree = [default_val] * (2 * self.size)
        self.lazy = [self.lazy_default_val] * (2 * self.size)

        # Build the segment tree
        for i in range(self.n):
            self.tree[self.size + i] = data[i]
        for i in range(self.size - 1, 0, -1):
            self.tree[i] = self.merge(self.tree[2 * i], self.tree[2 * i + 1])

    def push_down(self, node, node_left, node_right):
        """
        Push the lazy tag down to children nodes.
        """
        if self.lazy[node] != self.lazy_default_val:
            mid = (node_left + node_right) // 2

            # Update left child
            left_node = 2 * node
            self.tree[left_node] = self.update(
                self.tree[left_node], self.lazy[node], node_left, mid
            )
            if left_node < self.size:  # If not a leaf node
                self.lazy[left_node] = self.lazy_merge(
                    self.lazy[left_node], self.lazy[node]
                )

            # Update right child
            right_node = 2 * node + 1
            self.tree[right_node] = self.update(
                self.tree[right_node], self.lazy[node], mid + 1, node_right
            )
            if right_node < self.size:  # If not a leaf node
                self.lazy[right_node] = self.lazy_merge(
                    self.lazy[right_node], self.lazy[node]
                )

            # Clear current node's tag
            self.lazy[node] = self.lazy_default_val

    def range_query(self, l, r):
        """
        Query the value in range [l, r].
        """
        return self._range_query(1, 0, self.size - 1, l, r)

    def _range_query(self, node, node_left, node_right, query_l, query_r):
        """
        Helper function for recursive range query.
        """
        # No overlap
        if query_r < node_left or node_right < query_l:
            return None

        # Complete overlap
        if query_l <= node_left and node_right <= query_r:
            return self.tree[node]

        # Partial overlap - push down and query children
        self.push_down(node, node_left, node_right)

        mid = (node_left + node_right) // 2
        left_val = self._range_query(2 * node, node_left, mid, query_l, query_r)
        right_val = self._range_query(
            2 * node + 1, mid + 1, node_right, query_l, query_r
        )

        # Merge results from children
        if left_val is not None and right_val is not None:
            return self.merge(left_val, right_val)
        return left_val if left_val is not None else right_val

    def range_update(self, l, r, tag):
        """
        Update range [l, r] with the given tag.
        """
        self._range_update(1, 0, self.size - 1, l, r, tag)

    def _range_update(self, node, node_left, node_right, update_l, update_r, tag):
        """
        Helper function for recursive range update.
        """
        # No overlap
        if update_r < node_left or node_right < update_l:
            return

        # Complete overlap
        if update_l <= node_left and node_right <= update_r:
            self.tree[node] = self.update(self.tree[node], tag, node_left, node_right)
            if node < self.size:  # If not a leaf node
                self.lazy[node] = (
                    self.lazy_merge(self.lazy[node], tag)
                    if self.lazy[node] != self.lazy_default_val
                    else tag
                )
            return

        # Partial overlap - push down and update children
        self.push_down(node, node_left, node_right)

        mid = (node_left + node_right) // 2
        self._range_update(2 * node, node_left, mid, update_l, update_r, tag)
        self._range_update(2 * node + 1, mid + 1, node_right, update_l, update_r, tag)

        # Update current node with merged children
        self.tree[node] = self.merge(self.tree[2 * node], self.tree[2 * node + 1])
```