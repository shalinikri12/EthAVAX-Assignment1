// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract TaskManager {
    struct Task {
        string description;
        address assignee;
        bool completed;
    }

    mapping(uint256 => Task) public tasks;
    uint256 public taskCount;

    event TaskCreated(uint256 taskId, string description);
    event TaskAssigned(uint256 taskId, address assignee);
    event TaskCompleted(uint256 taskId);

    function createTask(string memory description) public {
        require(bytes(description).length > 0, "Description required");
        taskCount++;
        tasks[taskCount] = Task(description, address(0), false);
        emit TaskCreated(taskCount, description);
    }

    function assignTask(uint256 taskId, address assignee) public {
        Task storage task = tasks[taskId];
        require(task.assignee == address(0), "Task already assigned");
        task.assignee = assignee;
        emit TaskAssigned(taskId, assignee);
    }

    function completeTask(uint256 taskId) public {
        Task storage task = tasks[taskId];
        require(msg.sender == task.assignee, "Not the assignee");
        require(!task.completed, "Task already completed");
        task.completed = true;
        emit TaskCompleted(taskId);

        // Using assert to ensure task is marked as completed
        assert(task.completed == true);
    }

    function revokeTask(uint256 taskId) public {
        Task storage task = tasks[taskId];
        require(msg.sender == task.assignee, "Not the assignee");
        if (!task.completed) {
            revert("Task not completed");
        }
        task.assignee = address(0);
        task.completed = false;
        emit TaskAssigned(taskId, address(0));

        // Using assert to ensure task is marked as not completed
        assert(task.completed == false);
    }
}
