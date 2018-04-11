# BinarySearchTree
The first learning algorithm, the two fork tree, contains the notes I learned.


<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="utf-8">
	<title>二叉树 - BinarySearchTree</title>
</head>
<body>
<script>
	
function BinarySearchTree () {
	var Node = function (key) {
		this.key = key;
		this.left = null;
		this.right = null;
	},
	root = null;

	// 查看结果返回root
	this.getRoot = function() {
		return root;
	}

	// 向树中插入一个节点 - 主函数，向根节点插入函数
	this.insert = function( data ){
		var node = new Node(data);

		if(root === null ) {
			root = node;
		} else {
			insertNode(root, node);
		}
	}
	// 自平衡二叉树插入值
	this.AVLInsert = function( data ) {
		root = AVLInsertNode(root, data);
	}

	// 中序遍历
	this.inOrderTraverse = function (callback) {
		inOrderTraverseNode(root, callback);
	}
	// 先序遍历
	this.preOrderTraverse = function (callback) {
		preOrderTraverseNode(root, callback);
	}
	// 后序遍历
	this.postOrderTraverse = function(callback) {
		postOrderTraverseNode(root, callback);
	}

	// 搜索最小值
	this.min = function() {
		return minNode(root);
	}
	// 搜索最大值
	this.max = function() {
		return maxNode(root);
	}
	// 搜索指定值是否存在于于二叉树
	this.search = function( data) {
		return searchNode(root, data);
	}
	// 移除指定节点值
	this.remove = function (data) {
		root = removeNode(root, data);
	}

	// 自平衡二叉搜索树插入值
	var AVLInsertNode = function ( node, data) {
		if(node === null) {
			node = new Node(data);
		} else if (data < node.key) {
			node.left = AVLInsertNode(node.left, data);
			if( node.left !== null ){
				// 确认是否需要平衡
				if( (heightNode(node.left) - heightNode(node.right) ) > 1 ) {
					if (data < node.left.key) {
						node = rotationLL(node);
					} else  {
						node = rotationLR(node);
					}
				}
			}
		} else if (data > node.key) {
			node.right = AVLInsertNode(node.right, data);
			if(node.right !== null) {
				// 确认是否需要平衡
				if( (heightNode(node.right) - heightNode(node.left) ) > 1 ) {
					if (data > node.right.key) {
						node = rotationRR(node);
					} else {
						node = rotationRL(node);
					}
				}
			}
		}
		return node;
	}

}
// 插入函数的辅助函数 - 向子节点插入函数
var insertNode = function ( node, data ) {
	if(data.key < node.key) {
		if(node.left === null ){
			node.left = data;
		} else {
			insertNode( node.left, data);
		}
	} else {
		if(node.right === null ) {
			node.right = data;
		} else {
			insertNode(node.right, data);
		}
	}
}


var heightNode = function (node) {
	/*
	 * 自平衡因子：
	 *  	每个节点计算右子树高度和左子树高度的差，差值应为0、1、-1
	 * 		如果结果不是这三个值之一，则需要平衡该树
	 */
	 if(node === null) {
	 	return -1;
	 } else {
	 	return Math.max(heightNode(node.left) ),
	 		   heightNode(node.right) + 1;
	 }
}
var rotationRR = function (node) {	// 向左的单旋转
	var tmp = node.right;
	node.right = tmp.left;
	tmp.left = node;
	return tmp;
}
var rotationLL = function (node) {	// 向右的单旋转
	var tmp = node.left;
	node.left = tmp.right;
	tmp.right = node;
	return tmp;
}
var rotationLR = function (node) {	// 向右的双旋转
	node.left = rotationRR(node.left);
	return rotationLL(node);
}
var rotationRL = function (node) {	// 向左的双旋转
	node.right = rotationLL(node.right);
	return rotationRR(node);
}

// 中序遍历：从最小到最大的顺序访问所有节点，其中用法之一对树进行排序操作
var inOrderTraverseNode = function (node, callback) {
	/* 实现：
	 * 		判断根节点是否为空，不为空先将左侧二级节点及一下全部返回重新查询
	 * 		重新判断二级节点值是否为空，不为空则再次将左侧三级节点全部返回重新查询
	 * 		回调时将优先执行函数后再次向下执行，所以此时会进入到回调函数中再次执行一遍
	 * 		当找出最小数后停止向下，回上一层继续执行
	 */
	if(node !== null ) {
		inOrderTraverseNode(node.left, callback);
		callback(node.key);
		inOrderTraverseNode(node.right, callback);
	}
}
// 先序遍历 : 先访问节点本身，然后访问节点左侧值，后访问节点右侧值
var preOrderTraverseNode = function (node, callback) {
	if ( node !== null ) {
		// 实现同上
		callback(node.key);
		preOrderTraverseNode(node.left, callback);
		preOrderTraverseNode(node.right, callback);
	}
}
// 后序遍历 : 先访问节点的后代节点，在访问节点本身！ -  
// 			  应用之一，计算一个目录和子目录中所有文件所占空间的大小
var postOrderTraverseNode = function(node, callback) {
	if(node !== null) {
		// 实现同上
		postOrderTraverseNode(node.left, callback);
		postOrderTraverseNode(node.right, callback);
		callback(node.key);
	}
}

// 搜索最小值
var minNode = function (node) {
	/* 
	 * 该方法不接受用户传值，函数书写为二叉搜索树
	 * 比节点值小的值都存在左边
	 * 所以该函数通过直接向下查询左侧值最终返回最左侧值
	 */
	if(node) {
		while(node && node.left !== null){
			node = node.left;
		}
		return node.key;
	}
	return null;
}
// 搜索最大值  -  实现同上
var maxNode = function(node) {
	if(node){
		while(node && node.right !== null){
			node = node.right;
		}
		return node.key;
	}
	return null;
}
// 搜索指定值是否存在
var searchNode = function(node, data) {
	/* 
	 * 虽然这条判断值最开始，但其实执行到了最后
	 * 每次参数与节点值判断后都会重新传回节点进行判断
	 * 如果始终无法匹配到符合的值则返回第一条判断结果
	 */
	if(node === null) {
		return '没有这根树枝哦';
	}
	if(data < node.key) {
		return searchNode(node.left, data);
	} else if (data > node.key) {
		return searchNode(node.right, key);
	} else if(data === node.key) {    // 此处可省去if判断
		return true;
	}
}

// 移除指定节点
var removeNode = function(node, data) {
	if(node === null) {
		return null;
	}
	if(data < node.key) {
		node.left = removeNode(node.left, data);
		return node;
	} else if (data > node.key) {
		node.right = removeNode(node.right, key);
		return node;
	} else {  // 键等于node.key
		// 只有一个叶节点  -- 当前节点设置为空
		if(node.left === null && node.right === null){
			node = null;
			return node;
		}
		// 有一个子节点  -- 将子节点设置为当前节点
		if(node.left === null){
			node = node.right;
			return node;
		} else if (node.right === null) {
			node = node.left;
			return node;
		}
		// 有两个子节点  -- 将右侧节点设置为当前节点
		var aux = findMinNode(node.right);
		node.key = aux.key;
		node.right = removeNode(node.right, aux.key);
	}
}
var findMinNode = function (node) {
	while(node && node.left !== null) {
		node = node.left;
	}
	return node;
}

// 实例化二叉树，插入参数进行测试
var tree = new BinarySearchTree();

</script>
</body>
</html>
